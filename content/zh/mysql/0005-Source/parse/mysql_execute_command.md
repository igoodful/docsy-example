---
title: mysql_execute_command
description: mysql_execute_command
date: 2023-10-30
weight: 10001


---

{{< alert >}}


{{< /alert >}}

### sql/sql_parse.cc
```c++
int mysql_execute_command(THD *thd, bool first_level) {
  int res = false;
  LEX *const lex = thd->lex;
  /* first Query_block (have special meaning for many of non-SELECTcommands) */
  Query_block *const query_block = lex->query_block;
  /* first table of first Query_block */
  TABLE_LIST *const first_table = query_block->get_table_list();
  /* list of all tables in query */
  TABLE_LIST *all_tables;
  // keep GTID violation state in order to roll it back on statement failure
  bool gtid_consistency_violation_state = thd->has_gtid_consistency_violation;
  assert(query_block->master_query_expression() == lex->unit);
  DBUG_TRACE;
  /* EXPLAIN OTHER isn't explainable command, but can have describe flag. */
  assert(!lex->is_explain() || is_explainable_query(lex->sql_command) ||
         lex->sql_command == SQLCOM_EXPLAIN_OTHER);

  assert(!thd->m_transactional_ddl.inited() ||
         thd->in_active_multi_stmt_transaction());

  /*
    If there is a CREATE TABLE...START TRANSACTION command which
    is not yet committed or rollbacked, then we should allow only
    BINLOG INSERT, COMMIT or ROLLBACK command.
    TODO: Should we really check name of table when we cable BINLOG INSERT ?
  */
  if (thd->m_transactional_ddl.inited() && lex->sql_command != SQLCOM_COMMIT &&
      lex->sql_command != SQLCOM_ROLLBACK &&
      lex->sql_command != SQLCOM_BINLOG_BASE64_EVENT) {
    my_error(ER_STATEMENT_NOT_ALLOWED_AFTER_START_TRANSACTION, MYF(0));
    binlog_gtid_end_transaction(thd);
    return 1;
  }

  thd->work_part_info = nullptr;

  if (thd->optimizer_switch_flag(OPTIMIZER_SWITCH_SUBQUERY_TO_DERIVED))
    lex->add_statement_options(OPTION_NO_CONST_TABLES);

  /*
    Each statement or replication event which might produce deadlock
    should handle transaction rollback on its own. So by the start of
    the next statement transaction rollback request should be fulfilled
    already.
  */
  assert(!thd->transaction_rollback_request || thd->in_sub_stmt);
  /*
    In many cases first table of main Query_block have special meaning =>
    check that it is first table in global list and relink it first in
    queries_tables list if it is necessary (we need such relinking only
    for queries with subqueries in select list, in this case tables of
    subqueries will go to global list first)

    all_tables will differ from first_table only if most upper Query_block
    do not contain tables.

    Because of above in place where should be at least one table in most
    outer Query_block we have following check:
    assert(first_table == all_tables);
    assert(first_table == all_tables && first_table != 0);
  */
  lex->first_lists_tables_same();
  /* should be assigned after making first tables same */
  all_tables = lex->query_tables;
  /* set context for commands which do not use setup_tables */
  query_block->context.resolve_in_table_list_only(
      query_block->get_table_list());

  thd->get_stmt_da()->reset_diagnostics_area();
  if ((thd->lex->keep_diagnostics != DA_KEEP_PARSE_ERROR) &&
      (thd->lex->keep_diagnostics != DA_KEEP_DIAGNOSTICS)) {
    /*
      No parse errors, and it's not a diagnostic statement:
      remove the sql conditions from the DA!
      For diagnostic statements we need to keep the conditions
      around so we can inspec them.
    */
    thd->get_stmt_da()->reset_condition_info(thd);
  }

  if (thd->resource_group_ctx()->m_warn != 0) {
    auto res_grp_name = thd->resource_group_ctx()->m_switch_resource_group_str;
    switch (thd->resource_group_ctx()->m_warn) {
      case WARN_RESOURCE_GROUP_UNSUPPORTED: {
        auto res_grp_mgr = resourcegroups::Resource_group_mgr::instance();
        push_warning_printf(thd, Sql_condition::SL_WARNING,
                            ER_FEATURE_UNSUPPORTED,
                            ER_THD(thd, ER_FEATURE_UNSUPPORTED),
                            "Resource groups", res_grp_mgr->unsupport_reason());
        break;
      }
      case WARN_RESOURCE_GROUP_UNSUPPORTED_HINT:
        push_warning_printf(thd, Sql_condition::SL_WARNING,
                            ER_WARN_UNSUPPORTED_HINT,
                            ER_THD(thd, ER_WARN_UNSUPPORTED_HINT),
                            "Subquery or Stored procedure or Trigger");
        break;
      case WARN_RESOURCE_GROUP_TYPE_MISMATCH: {
        ulonglong pfs_thread_id = 0;
        /*
          Resource group is unsupported with DISABLE_PSI_THREAD.
          The below #ifdef is required for compilation when DISABLE_PSI_THREAD
          is enabled.
        */
#ifdef HAVE_PSI_THREAD_INTERFACE
        pfs_thread_id = PSI_THREAD_CALL(get_current_thread_internal_id)();
#endif  // HAVE_PSI_THREAD_INTERFACE
        push_warning_printf(thd, Sql_condition::SL_WARNING,
                            ER_RESOURCE_GROUP_BIND_FAILED,
                            ER_THD(thd, ER_RESOURCE_GROUP_BIND_FAILED),
                            res_grp_name, pfs_thread_id,
                            "System resource group can't be bound"
                            " with a session thread");
        break;
      }
      case WARN_RESOURCE_GROUP_NOT_EXISTS:
        push_warning_printf(
            thd, Sql_condition::SL_WARNING, ER_RESOURCE_GROUP_NOT_EXISTS,
            ER_THD(thd, ER_RESOURCE_GROUP_NOT_EXISTS), res_grp_name);
        break;
      case WARN_RESOURCE_GROUP_ACCESS_DENIED:
        push_warning_printf(thd, Sql_condition::SL_WARNING,
                            ER_SPECIFIC_ACCESS_DENIED_ERROR,
                            ER_THD(thd, ER_SPECIFIC_ACCESS_DENIED_ERROR),
                            "SUPER OR RESOURCE_GROUP_ADMIN OR "
                            "RESOURCE_GROUP_USER");
    }
    thd->resource_group_ctx()->m_warn = 0;
    res_grp_name[0] = '\0';
  }

  if (unlikely(thd->slave_thread)) {
    if (!check_database_filters(thd, thd->db().str, lex->sql_command)) {
      binlog_gtid_end_transaction(thd);
      return 0;
    }

    if (lex->sql_command == SQLCOM_DROP_TRIGGER) {
      /*
        When dropping a trigger, we need to load its table name
        before checking slave filter rules.
      */
      TABLE_LIST *trigger_table = nullptr;
      (void)get_table_for_trigger(thd, lex->spname->m_db, lex->spname->m_name,
                                  true, &trigger_table);
      if (trigger_table != nullptr) {
        lex->add_to_query_tables(trigger_table);
        all_tables = trigger_table;
      } else {
        /*
          If table name cannot be loaded,
          it means the trigger does not exists possibly because
          CREATE TRIGGER was previously skipped for this trigger
          according to slave filtering rules.
          Returning success without producing any errors in this case.
        */
        binlog_gtid_end_transaction(thd);
        return 0;
      }

      // force searching in slave.cc:tables_ok()
      all_tables->updating = true;
    }

    /*
      For fix of BUG#37051, the master stores the table map for update
      in the Query_log_event, and the value is assigned to
      thd->table_map_for_update before executing the update
      query.

      If thd->table_map_for_update is set, then we are
      replicating from a new master, we can use this value to apply
      filter rules without opening all the tables. However If
      thd->table_map_for_update is not set, then we are
      replicating from an old master, so we just skip this and
      continue with the old method. And of course, the bug would still
      exist for old masters.
    */
    if (lex->sql_command == SQLCOM_UPDATE_MULTI && thd->table_map_for_update) {
      table_map table_map_for_update = thd->table_map_for_update;
      uint nr = 0;
      TABLE_LIST *table;
      for (table = all_tables; table; table = table->next_global, nr++) {
        if (table_map_for_update & ((table_map)1 << nr))
          table->updating = true;
        else
          table->updating = false;
      }

      if (all_tables_not_ok(thd, all_tables)) {
        /* we warn the slave SQL thread */
        my_error(ER_SLAVE_IGNORED_TABLE, MYF(0));
        binlog_gtid_end_transaction(thd);
        return 0;
      }

      for (table = all_tables; table; table = table->next_global)
        table->updating = true;
    }

    /*
      Check if statement should be skipped because of slave filtering
      rules

      Exceptions are:
      - UPDATE MULTI: For this statement, we want to check the filtering
        rules later in the code
      - SET: we always execute it (Not that many SET commands exists in
        the binary log anyway -- only 4.1 masters write SET statements,
        in 5.0 there are no SET statements in the binary log)
      - DROP TEMPORARY TABLE IF EXISTS: we always execute it (otherwise we
        have stale files on slave caused by exclusion of one tmp table).
    */
    if (!(lex->sql_command == SQLCOM_UPDATE_MULTI) &&
        !(lex->sql_command == SQLCOM_SET_OPTION) &&
        !(lex->sql_command == SQLCOM_DROP_TABLE && lex->drop_temporary &&
          lex->drop_if_exists) &&
        all_tables_not_ok(thd, all_tables)) {
      /* we warn the slave SQL thread */
      my_error(ER_SLAVE_IGNORED_TABLE, MYF(0));
      binlog_gtid_end_transaction(thd);
      return 0;
    }
    /*
       Execute deferred events first
    */
    if (slave_execute_deferred_events(thd)) return -1;

    int ret = launch_hook_trans_begin(thd, all_tables);
    if (ret) {
      my_error(ret, MYF(0));
      return -1;
    }

  } else {
    int ret = launch_hook_trans_begin(thd, all_tables);
    if (ret) {
      my_error(ret, MYF(0));
      return -1;
    }

    /*
      When option readonly is set deny operations which change non-temporary
      tables. Except for the replication thread and the 'super' users.
    */
    if (deny_updates_if_read_only_option(thd, all_tables)) {
      err_readonly(thd);
      return -1;
    }
  } /* endif unlikely slave */

  thd->status_var.com_stat[lex->sql_command]++;

  Opt_trace_start ots(thd, all_tables, lex->sql_command, &lex->var_list,
                      thd->query().str, thd->query().length, nullptr,
                      thd->variables.character_set_client);

  Opt_trace_object trace_command(&thd->opt_trace);
  Opt_trace_array trace_command_steps(&thd->opt_trace, "steps");

  if (lex->m_sql_cmd && lex->m_sql_cmd->owner())
    lex->m_sql_cmd->owner()->trace_parameter_types();

  assert(thd->get_transaction()->cannot_safely_rollback(
             Transaction_ctx::STMT) == false);

  switch (gtid_pre_statement_checks(thd)) {
    case GTID_STATEMENT_EXECUTE:
      break;
    case GTID_STATEMENT_CANCEL:
      return -1;
    case GTID_STATEMENT_SKIP:
      my_ok(thd);
      binlog_gtid_end_transaction(thd);
      return 0;
  }

  if (thd->variables.require_row_format) {
    if (evaluate_command_row_only_restrictions(thd)) {
      my_error(ER_CLIENT_QUERY_FAILURE_INVALID_NON_ROW_FORMAT, MYF(0));
      return -1;
    }
  }

  /*
    End a active transaction so that this command will have it's
    own transaction and will also sync the binary log. If a DDL is
    not run in it's own transaction it may simply never appear on
    the slave in case the outside transaction rolls back.
  */
  if (stmt_causes_implicit_commit(thd, CF_IMPLICIT_COMMIT_BEGIN)) {
    /*
      Note that this should never happen inside of stored functions
      or triggers as all such statements prohibited there.
    */
    assert(!thd->in_sub_stmt);
    /* Statement transaction still should not be started. */
    assert(thd->get_transaction()->is_empty(Transaction_ctx::STMT));

    /*
      Implicit commit is not allowed with an active XA transaction.
      In this case we should not release metadata locks as the XA transaction
      will not be rolled back. Therefore we simply return here.
    */
    if (trans_check_state(thd)) return -1;

    /* Commit the normal transaction if one is active. */
    if (trans_commit_implicit(thd)) return -1;
    /* Release metadata locks acquired in this transaction. */
    thd->mdl_context.release_transactional_locks();
  }

  DEBUG_SYNC(thd, "after_implicit_pre_commit");

  if (gtid_pre_statement_post_implicit_commit_checks(thd)) return -1;

  if (mysql_audit_notify(thd,
                         first_level ? MYSQL_AUDIT_QUERY_START
                                     : MYSQL_AUDIT_QUERY_NESTED_START,
                         first_level ? "MYSQL_AUDIT_QUERY_START"
                                     : "MYSQL_AUDIT_QUERY_NESTED_START")) {
    return 1;
  }

#ifndef NDEBUG
  if (lex->sql_command != SQLCOM_SET_OPTION)
    DEBUG_SYNC(thd, "before_execute_sql_command");
#endif

  /*
    Start a new transaction if CREATE TABLE has START TRANSACTION clause.
    Disable binlog so that the BEGIN is not logged in binlog.
   */
  if (lex->create_info && lex->create_info->m_transactional_ddl &&
      !thd->slave_thread) {
    Disable_binlog_guard binlog_guard(thd);
    if (trans_begin(thd, MYSQL_START_TRANS_OPT_READ_WRITE)) return true;
  }

  /*
    For statements which need this, prevent InnoDB from automatically
    committing InnoDB transaction each time data-dictionary tables are
    closed after being updated.
  */
  Disable_autocommit_guard autocommit_guard(
      sqlcom_needs_autocommit_off(lex) && !thd->is_plugin_fake_ddl() ? thd
                                                                     : nullptr);

  /*
    Check if we are in a read-only transaction and we're trying to
    execute a statement which should always be disallowed in such cases.

    Note that this check is done after any implicit commits.
  */
  if (thd->tx_read_only &&
      (sql_command_flags[lex->sql_command] & CF_DISALLOW_IN_RO_TRANS)) {
    my_error(ER_CANT_EXECUTE_IN_READ_ONLY_TRANSACTION, MYF(0));
    goto error;
  }

  /*
    Close tables open by HANDLERs before executing DDL statement
    which is going to affect those tables.

    This should happen before temporary tables are pre-opened as
    otherwise we will get errors about attempt to re-open tables
    if table to be changed is open through HANDLER.

    Note that even although this is done before any privilege
    checks there is no security problem here as closing open
    HANDLER doesn't require any privileges anyway.
  */
  if (sql_command_flags[lex->sql_command] & CF_HA_CLOSE)
    mysql_ha_rm_tables(thd, all_tables);

  /*
    Check that the command is allowed on the PROTOCOL_PLUGIN
  */
  if (thd->get_protocol()->type() == Protocol::PROTOCOL_PLUGIN &&
      !(sql_command_flags[lex->sql_command] & CF_ALLOW_PROTOCOL_PLUGIN)) {
    my_error(ER_PLUGGABLE_PROTOCOL_COMMAND_NOT_SUPPORTED, MYF(0));
    goto error;
  }

  /*
    Pre-open temporary tables to simplify privilege checking
    for statements which need this.
  */
  if (sql_command_flags[lex->sql_command] & CF_PREOPEN_TMP_TABLES) {
    if (open_temporary_tables(thd, all_tables)) goto error;
  }

  // Save original info for EXPLAIN FOR CONNECTION
  if (!thd->in_sub_stmt)
    thd->query_plan.set_query_plan(lex->sql_command, lex,
                                   !thd->stmt_arena->is_regular());

  /* Update system variables specified in SET_VAR hints. */
  if (lex->opt_hints_global && lex->opt_hints_global->sys_var_hint)
    lex->opt_hints_global->sys_var_hint->update_vars(thd);

  /* Check if the statement fulfill the requirements on ACL CACHE */
  if (!command_satisfy_acl_cache_requirement(lex->sql_command)) {
    my_error(ER_OPTION_PREVENTS_STATEMENT, MYF(0), "--skip-grant-tables");
    goto error;
  }

  DBUG_EXECUTE_IF(
      "force_rollback_in_replica_on_transactional_ddl_commit",
      if (thd->m_transactional_ddl.inited() &&
          thd->lex->sql_command == SQLCOM_COMMIT) {
        lex->sql_command = SQLCOM_ROLLBACK;
      });

  /*
    We do not flag "is DML" (TX_STMT_DML) here as replication expects us to
    test for LOCK TABLE etc. first. To rephrase, we try not to set TX_STMT_DML
    until we have the MDL, and LOCK TABLE could massively delay this.
  */

  switch (lex->sql_command) {
    case SQLCOM_PREPARE: {
      mysql_sql_stmt_prepare(thd);
      break;
    }
    case SQLCOM_EXECUTE: {
      mysql_sql_stmt_execute(thd);
      break;
    }
    case SQLCOM_DEALLOCATE_PREPARE: {
      mysql_sql_stmt_close(thd);
      break;
    }

    case SQLCOM_EMPTY_QUERY:
      my_ok(thd);
      break;

    case SQLCOM_HELP:
      res = mysqld_help(thd, lex->help_arg);
      break;

    case SQLCOM_PURGE: {
      Security_context *sctx = thd->security_context();
      if (!sctx->check_access(SUPER_ACL) &&
          !sctx->has_global_grant(STRING_WITH_LEN("BINLOG_ADMIN")).first) {
        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                 "SUPER or BINLOG_ADMIN");
        goto error;
      }
      /* PURGE MASTER LOGS TO 'file' */
      res = purge_master_logs(thd, lex->to_log);
      break;
    }
    case SQLCOM_PURGE_BEFORE: {
      Item *it;
      Security_context *sctx = thd->security_context();
      if (!sctx->check_access(SUPER_ACL) &&
          !sctx->has_global_grant(STRING_WITH_LEN("BINLOG_ADMIN")).first) {
        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                 "SUPER or BINLOG_ADMIN");
        goto error;
      }
      /* PURGE MASTER LOGS BEFORE 'data' */
      it = lex->purge_value_list.head();
      if ((!it->fixed && it->fix_fields(lex->thd, &it)) || it->check_cols(1)) {
        my_error(ER_WRONG_ARGUMENTS, MYF(0), "PURGE LOGS BEFORE");
        goto error;
      }
      it = new Item_func_unix_timestamp(it);
      /*
        it is OK only emulate fix_fieds, because we need only
        value of constant
      */
      it->quick_fix_field();
      time_t purge_time = static_cast<time_t>(it->val_int());
      if (thd->is_error()) goto error;
      res = purge_master_logs_before_date(thd, purge_time);
      break;
    }
    case SQLCOM_CHANGE_MASTER: {
      Security_context *sctx = thd->security_context();
      if (!sctx->check_access(SUPER_ACL) &&
          !sctx->has_global_grant(STRING_WITH_LEN("REPLICATION_SLAVE_ADMIN"))
               .first) {
        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                 "SUPER or REPLICATION_SLAVE_ADMIN");
        goto error;
      }
      res = change_master_cmd(thd);
      break;
    }
    case SQLCOM_START_GROUP_REPLICATION: {
      Security_context *sctx = thd->security_context();
      if (!sctx->check_access(SUPER_ACL) &&
          !sctx->has_global_grant(STRING_WITH_LEN("GROUP_REPLICATION_ADMIN"))
               .first) {
        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                 "SUPER or GROUP_REPLICATION_ADMIN");
        goto error;
      }
      if (lex->slave_connection.password && !lex->slave_connection.user) {
        my_error(ER_GROUP_REPLICATION_USER_MANDATORY_MSG, MYF(0));
        goto error;
      }

      /*
        If the client thread has locked tables, a deadlock is possible.
        Assume that
        - the client thread does LOCK TABLE t READ.
        - then the client thread does START GROUP_REPLICATION.
             -try to make the server in super ready only mode
             -acquire MDL lock ownership which will be waiting for
              LOCK on table t to be released.
        To prevent that, refuse START GROUP_REPLICATION if the
        client thread has locked tables
      */
      if (thd->locked_tables_mode || thd->in_active_multi_stmt_transaction() ||
          thd->in_sub_stmt) {
        my_error(ER_LOCK_OR_ACTIVE_TRANSACTION, MYF(0));
        goto error;
      }

      if (Clone_handler::is_provisioning()) {
        my_error(ER_GROUP_REPLICATION_COMMAND_FAILURE, MYF(0),
                 "START GROUP_REPLICATION",
                 "This server is being provisioned by CLONE INSTANCE, "
                 "please wait until it is complete.");
        goto error;
      }

      char *error_message = nullptr;
      res = group_replication_start(&error_message, thd);

      // To reduce server dependency, server errors are not used here
      switch (res) {
        case 1:  // GROUP_REPLICATION_CONFIGURATION_ERROR
          my_error(ER_GROUP_REPLICATION_CONFIGURATION, MYF(0));
          goto error;
        case 2:  // GROUP_REPLICATION_ALREADY_RUNNING
          my_error(ER_GROUP_REPLICATION_RUNNING, MYF(0));
          goto error;
        case 3:  // GROUP_REPLICATION_REPLICATION_APPLIER_INIT_ERROR
          my_error(ER_GROUP_REPLICATION_APPLIER_INIT_ERROR, MYF(0));
          goto error;
        case 4:  // GROUP_REPLICATION_COMMUNICATION_LAYER_SESSION_ERROR
          my_error(ER_GROUP_REPLICATION_COMMUNICATION_LAYER_SESSION_ERROR,
                   MYF(0));
          goto error;
        case 5:  // GROUP_REPLICATION_COMMUNICATION_LAYER_JOIN_ERROR
          my_error(ER_GROUP_REPLICATION_COMMUNICATION_LAYER_JOIN_ERROR, MYF(0));
          goto error;
        case 7:  // GROUP_REPLICATION_MAX_GROUP_SIZE
          my_error(ER_GROUP_REPLICATION_MAX_GROUP_SIZE, MYF(0));
          goto error;
        case 8:  // GROUP_REPLICATION_COMMAND_FAILURE
          if (error_message == nullptr) {
            my_error(ER_GROUP_REPLICATION_COMMAND_FAILURE, MYF(0),
                     "START GROUP_REPLICATION",
                     "Please check error log for additional details.");
          } else {
            my_error(ER_GROUP_REPLICATION_COMMAND_FAILURE, MYF(0),
                     "START GROUP_REPLICATION", error_message);
            my_free(error_message);
          }
          goto error;
        case 9:  // GROUP_REPLICATION_SERVICE_MESSAGE_INIT_FAILURE
          my_error(ER_GRP_RPL_MESSAGE_SERVICE_INIT_FAILURE, MYF(0));
          goto error;
        case 10:  // GROUP_REPLICATION_RECOVERY_CHANNEL_STILL_RUNNING
          my_error(ER_GRP_RPL_RECOVERY_CHANNEL_STILL_RUNNING, MYF(0));
          goto error;
      }
      my_ok(thd);
      res = 0;
      break;
    }

    case SQLCOM_STOP_GROUP_REPLICATION: {
      Security_context *sctx = thd->security_context();
      if (!sctx->check_access(SUPER_ACL) &&
          !sctx->has_global_grant(STRING_WITH_LEN("GROUP_REPLICATION_ADMIN"))
               .first) {
        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                 "SUPER or GROUP_REPLICATION_ADMIN");
        goto error;
      }

      /*
        Please see explanation @SQLCOM_SLAVE_STOP case
        to know the reason for thd->locked_tables_mode in
        the below if condition.
      */
      if (thd->locked_tables_mode || thd->in_active_multi_stmt_transaction() ||
          thd->in_sub_stmt) {
        my_error(ER_LOCK_OR_ACTIVE_TRANSACTION, MYF(0));
        goto error;
      }

      char *error_message = nullptr;
      res = group_replication_stop(&error_message);
      if (res == 1)  // GROUP_REPLICATION_CONFIGURATION_ERROR
      {
        my_error(ER_GROUP_REPLICATION_CONFIGURATION, MYF(0));
        goto error;
      }
      if (res == 6)  // GROUP_REPLICATION_APPLIER_THREAD_TIMEOUT
      {
        my_error(ER_GROUP_REPLICATION_STOP_APPLIER_THREAD_TIMEOUT, MYF(0));
        goto error;
      }
      if (res == 8)  // GROUP_REPLICATION_COMMAND_FAILURE
      {
        if (error_message == nullptr) {
          my_error(ER_GROUP_REPLICATION_COMMAND_FAILURE, MYF(0),
                   "STOP GROUP_REPLICATION",
                   "Please check error log for additonal details.");
        } else {
          my_error(ER_GROUP_REPLICATION_COMMAND_FAILURE, MYF(0),
                   "STOP GROUP_REPLICATION", error_message);
          my_free(error_message);
        }
        goto error;
      }
      if (res == 11)  // GROUP_REPLICATION_STOP_WITH_RECOVERY_TIMEOUT
        push_warning(thd, Sql_condition::SL_WARNING,
                     ER_GRP_RPL_RECOVERY_CHANNEL_STILL_RUNNING,
                     ER_THD(thd, ER_GRP_RPL_RECOVERY_CHANNEL_STILL_RUNNING));

      my_ok(thd);
      res = 0;
      break;
    }

    case SQLCOM_SLAVE_START: {
      res = start_slave_cmd(thd);
      break;
    }
    case SQLCOM_SLAVE_STOP: {
      /*
        If the client thread has locked tables, a deadlock is possible.
        Assume that
        - the client thread does LOCK TABLE t READ.
        - then the master updates t.
        - then the SQL slave thread wants to update t,
          so it waits for the client thread because t is locked by it.
        - then the client thread does SLAVE STOP.
          SLAVE STOP waits for the SQL slave thread to terminate its
          update t, which waits for the client thread because t is locked by it.
        To prevent that, refuse SLAVE STOP if the
        client thread has locked tables
      */
      if (thd->locked_tables_mode || thd->in_active_multi_stmt_transaction() ||
          thd->global_read_lock.is_acquired()) {
        my_error(ER_LOCK_OR_ACTIVE_TRANSACTION, MYF(0));
        goto error;
      }

      res = stop_slave_cmd(thd);
      break;
    }
    case SQLCOM_RENAME_TABLE: {
      assert(first_table == all_tables && first_table != nullptr);
      TABLE_LIST *table;
      for (table = first_table; table; table = table->next_local->next_local) {
        if (check_access(thd, ALTER_ACL | DROP_ACL, table->db,
                         &table->grant.privilege, &table->grant.m_internal,
                         false, false) ||
            check_access(thd, INSERT_ACL | CREATE_ACL, table->next_local->db,
                         &table->next_local->grant.privilege,
                         &table->next_local->grant.m_internal, false, false))
          goto error;

        TABLE_LIST old_list = table[0];
        TABLE_LIST new_list = table->next_local[0];
        /*
          It's not clear what the above assignments actually want to
          accomplish. What we do know is that they do *not* want to copy the MDL
          requests, so we overwrite them with uninitialized request.
        */
        old_list.mdl_request = MDL_request();
        new_list.mdl_request = MDL_request();

        if (check_grant(thd, ALTER_ACL | DROP_ACL, &old_list, false, 1,
                        false) ||
            (!test_all_bits(table->next_local->grant.privilege,
                            INSERT_ACL | CREATE_ACL) &&
             check_grant(thd, INSERT_ACL | CREATE_ACL, &new_list, false, 1,
                         false)))
          goto error;
      }

      if (mysql_rename_tables(thd, first_table)) goto error;
      break;
    }
    case SQLCOM_CHECKSUM: {
      assert(first_table == all_tables && first_table != nullptr);
      if (check_table_access(thd, SELECT_ACL, all_tables, false, UINT_MAX,
                             false))
        goto error; /* purecov: inspected */

      res = mysql_checksum_table(thd, first_table, &lex->check_opt);
      break;
    }
    case SQLCOM_REPLACE:
    case SQLCOM_INSERT:
    case SQLCOM_REPLACE_SELECT:
    case SQLCOM_INSERT_SELECT:
    case SQLCOM_DELETE:
    case SQLCOM_DELETE_MULTI:
    case SQLCOM_UPDATE:
    case SQLCOM_UPDATE_MULTI:
    case SQLCOM_CREATE_TABLE:
    case SQLCOM_CREATE_INDEX:
    case SQLCOM_DROP_INDEX:
    case SQLCOM_ASSIGN_TO_KEYCACHE:
    case SQLCOM_PRELOAD_KEYS:
    case SQLCOM_LOAD: {
      assert(first_table == all_tables && first_table != nullptr);
      assert(lex->m_sql_cmd != nullptr);
      res = lex->m_sql_cmd->execute(thd);
      break;
    }
    case SQLCOM_DROP_TABLE: {
      assert(first_table == all_tables && first_table != nullptr);
      if (!lex->drop_temporary) {
        if (check_table_access(thd, DROP_ACL, all_tables, false, UINT_MAX,
                               false))
          goto error; /* purecov: inspected */
      }
      /* DDL and binlog write order are protected by metadata locks. */
      res = mysql_rm_table(thd, first_table, lex->drop_if_exists,
                           lex->drop_temporary);
      /* when dropping temporary tables if @@session_track_state_change is ON
         then send the boolean tracker in the OK packet */
      if (!res && lex->drop_temporary) {
        if (thd->session_tracker.get_tracker(SESSION_STATE_CHANGE_TRACKER)
                ->is_enabled())
          thd->session_tracker.get_tracker(SESSION_STATE_CHANGE_TRACKER)
              ->mark_as_changed(thd, nullptr);
      }
    } break;
    case SQLCOM_CHANGE_DB: {
      const LEX_CSTRING db_str = {query_block->db, strlen(query_block->db)};

      if (!mysql_change_db(thd, db_str, false)) my_ok(thd);

      break;
    }

    case SQLCOM_SET_OPTION: {
      List<set_var_base> *lex_var_list = &lex->var_list;

      if (check_table_access(thd, SELECT_ACL, all_tables, false, UINT_MAX,
                             false))
        goto error;

      if (open_tables_for_query(thd, all_tables, false)) goto error;
      if (!thd->stmt_arena->is_regular()) {
        lex->restore_cmd_properties();
        bind_fields(thd->stmt_arena->item_list());
        if (all_tables != nullptr &&
            !thd->stmt_arena->is_stmt_prepare_or_first_stmt_execute() &&
            query_block->check_privileges_for_subqueries(thd))
          return true;
      }
      if (!(res = sql_set_variables(thd, lex_var_list, true)))
        my_ok(thd);
      else {
        /*
          We encountered some sort of error, but no message was sent.
          Send something semi-generic here since we don't know which
          assignment in the list caused the error.
        */
        if (!thd->is_error()) my_error(ER_WRONG_ARGUMENTS, MYF(0), "SET");
        goto error;
      }

#ifndef NDEBUG
      /*
        Makes server crash when executing SET SESSION debug = 'd,crash_now';
        See mysql-test/include/dbug_crash[_all].inc
      */
      const bool force_server_crash_dbug = false;
      DBUG_EXECUTE_IF("crash_now", assert(force_server_crash_dbug););
      DBUG_EXECUTE_IF("crash_now_safe", DBUG_SUICIDE(););
#endif

      break;
    }
    case SQLCOM_SET_PASSWORD: {
      List<set_var_base> *lex_var_list = &lex->var_list;

      assert(lex_var_list->elements == 1);
      assert(all_tables == nullptr);
      Userhostpassword_list generated_passwords;
      if (!(res = sql_set_variables(thd, lex_var_list, false))) {
        List_iterator_fast<set_var_base> it(*lex_var_list);
        set_var_base *var;
        while ((var = it++)) {
          set_var_password *setpasswd = static_cast<set_var_password *>(var);
          if (setpasswd->has_generated_password()) {
            const LEX_USER *user = setpasswd->get_user();
            generated_passwords.push_back(
                {std::string(user->user.str, user->user.length),
                 std::string(user->host.str, user->host.length),
                 setpasswd->get_generated_password()});
          }
        }
        if (generated_passwords.size() > 0) {
          if (send_password_result_set(thd, generated_passwords)) goto error;
        }  // end if generated_passwords
        if (generated_passwords.size() == 0) my_ok(thd);
      } else {
        // We encountered some sort of error, but no message was sent.
        if (!thd->is_error())
          my_error(ER_WRONG_ARGUMENTS, MYF(0), "SET PASSWORD");
        goto error;
      }

      break;
    }

    case SQLCOM_UNLOCK_TABLES:
      /*
        It is critical for mysqldump --single-transaction --source-data that
        UNLOCK TABLES does not implicitely commit a connection which has only
        done FLUSH TABLES WITH READ LOCK + BEGIN. If this assumption becomes
        false, mysqldump will not work.
      */
      if (thd->variables.option_bits & OPTION_TABLE_LOCK) {
        /*
          Can we commit safely? If not, return to avoid releasing
          transactional metadata locks.
        */
        if (trans_check_state(thd)) return -1;
        res = trans_commit_implicit(thd);
        thd->locked_tables_list.unlock_locked_tables(thd);
        thd->mdl_context.release_transactional_locks();
        thd->variables.option_bits &= ~(OPTION_TABLE_LOCK);
      }
      if (thd->global_read_lock.is_acquired())
        thd->global_read_lock.unlock_global_read_lock(thd);
      if (res) goto error;
      my_ok(thd);
      break;
    case SQLCOM_LOCK_TABLES:
      /*
        Can we commit safely? If not, return to avoid releasing
        transactional metadata locks.
      */
      if (trans_check_state(thd)) return -1;
      /* We must end the transaction first, regardless of anything */
      res = trans_commit_implicit(thd);
      thd->locked_tables_list.unlock_locked_tables(thd);
      /* Release transactional metadata locks. */
      thd->mdl_context.release_transactional_locks();
      if (res) goto error;

      /*
        Here we have to pre-open temporary tables for LOCK TABLES.

        CF_PREOPEN_TMP_TABLES is not set for this SQL statement simply
        because LOCK TABLES calls close_thread_tables() as a first thing
        (it's called from unlock_locked_tables() above). So even if
        CF_PREOPEN_TMP_TABLES was set and the tables would be pre-opened
        in a usual way, they would have been closed.
      */

      if (open_temporary_tables(thd, all_tables)) goto error;

      if (lock_tables_precheck(thd, all_tables)) goto error;

      thd->variables.option_bits |= OPTION_TABLE_LOCK;

      res = lock_tables_open_and_lock_tables(thd, all_tables);

      if (res) {
        thd->variables.option_bits &= ~(OPTION_TABLE_LOCK);
      } else {
        my_ok(thd);
      }
      break;

    case SQLCOM_IMPORT:
      res = lex->m_sql_cmd->execute(thd);
      break;
    case SQLCOM_CREATE_DB: {
      const char *alias;
      if (!(alias = thd->strmake(lex->name.str, lex->name.length)) ||
          (check_and_convert_db_name(&lex->name, false) !=
           Ident_name_check::OK))
        break;
      if (check_access(thd, CREATE_ACL, lex->name.str, nullptr, nullptr, true,
                       false))
        break;
      /*
        As mysql_create_db() may modify HA_CREATE_INFO structure passed to
        it, we need to use a copy of LEX::create_info to make execution
        prepared statement- safe.
      */
      HA_CREATE_INFO create_info(*lex->create_info);
      res = mysql_create_db(
          thd, (lower_case_table_names == 2 ? alias : lex->name.str),
          &create_info);
      break;
    }
    case SQLCOM_DROP_DB: {
      if (check_and_convert_db_name(&lex->name, false) != Ident_name_check::OK)
        break;
      if (check_access(thd, DROP_ACL, lex->name.str, nullptr, nullptr, true,
                       false))
        break;
      res = mysql_rm_db(thd, to_lex_cstring(lex->name), lex->drop_if_exists);
      break;
    }
    case SQLCOM_ALTER_DB: {
      if (check_and_convert_db_name(&lex->name, false) != Ident_name_check::OK)
        break;
      if (check_access(thd, ALTER_ACL, lex->name.str, nullptr, nullptr, true,
                       false))
        break;
      /*
        As mysql_alter_db() may modify HA_CREATE_INFO structure passed to
        it, we need to use a copy of LEX::create_info to make execution
        prepared statement- safe.
      */
      HA_CREATE_INFO create_info(*lex->create_info);
      res = mysql_alter_db(thd, lex->name.str, &create_info);
      break;
    }
    case SQLCOM_CREATE_EVENT:
    case SQLCOM_ALTER_EVENT:
      do {
        assert(lex->event_parse_data);
        if (lex->table_or_sp_used()) {
          my_error(ER_NOT_SUPPORTED_YET, MYF(0),
                   "Usage of subqueries or stored "
                   "function calls as part of this statement");
          break;
        }

        res = sp_process_definer(thd);
        if (res) break;

        switch (lex->sql_command) {
          case SQLCOM_CREATE_EVENT: {
            bool if_not_exists =
                (lex->create_info->options & HA_LEX_CREATE_IF_NOT_EXISTS);
            res =
                Events::create_event(thd, lex->event_parse_data, if_not_exists);
            break;
          }
          case SQLCOM_ALTER_EVENT: {
            LEX_CSTRING name_lex_str = NULL_CSTR;
            if (lex->spname) {
              name_lex_str.str = lex->spname->m_name.str;
              name_lex_str.length = lex->spname->m_name.length;
            }

            res =
                Events::update_event(thd, lex->event_parse_data,
                                     lex->spname ? &lex->spname->m_db : nullptr,
                                     lex->spname ? &name_lex_str : nullptr);
            break;
          }
          default:
            assert(0);
        }
        DBUG_PRINT("info", ("DDL error code=%d", res));
        if (!res && !thd->killed) my_ok(thd);

      } while (false);
      /* Don't do it, if we are inside a SP */
      if (!thd->sp_runtime_ctx) {
        sp_head::destroy(lex->sphead);
        lex->sphead = nullptr;
      }
      /* lex->cleanup() is called outside, no need to call it here */
      break;
    case SQLCOM_DROP_EVENT: {
      if (!(res = Events::drop_event(thd, lex->spname->m_db,
                                     to_lex_cstring(lex->spname->m_name),
                                     lex->drop_if_exists)))
        my_ok(thd);
      break;
    }
    case SQLCOM_CREATE_FUNCTION:  // UDF function
    {
      if (check_access(thd, INSERT_ACL, "mysql", nullptr, nullptr, true, false))
        break;
      if (!(res = mysql_create_function(thd, &lex->udf))) my_ok(thd);
      break;
    }
    case SQLCOM_CREATE_USER: {
      if (check_access(thd, INSERT_ACL, "mysql", nullptr, nullptr, true,
                       true) &&
          check_global_access(thd, CREATE_USER_ACL))
        break;
      /* Conditionally writes to binlog */
      HA_CREATE_INFO create_info(*lex->create_info);
      if (!(res = mysql_create_user(
                thd, lex->users_list,
                create_info.options & HA_LEX_CREATE_IF_NOT_EXISTS, false))) {
        // OK or result set was already sent.
      }

      break;
    }
    case SQLCOM_DROP_USER: {
      if (check_access(thd, DELETE_ACL, "mysql", nullptr, nullptr, true,
                       true) &&
          check_global_access(thd, CREATE_USER_ACL))
        break;
      /* Conditionally writes to binlog */
      if (!(res = mysql_drop_user(thd, lex->users_list, lex->drop_if_exists,
                                  false)))
        my_ok(thd);

      break;
    }
    case SQLCOM_RENAME_USER: {
      if (check_access(thd, UPDATE_ACL, "mysql", nullptr, nullptr, true,
                       true) &&
          check_global_access(thd, CREATE_USER_ACL))
        break;
      /* Conditionally writes to binlog */
      if (!(res = mysql_rename_user(thd, lex->users_list))) my_ok(thd);
      break;
    }
    case SQLCOM_REVOKE_ALL: {
      if (check_access(thd, UPDATE_ACL, "mysql", nullptr, nullptr, true,
                       true) &&
          check_global_access(thd, CREATE_USER_ACL))
        break;

      /* Replicate current user as grantor */
      thd->binlog_invoker();

      /* Conditionally writes to binlog */
      if (!(res = mysql_revoke_all(thd, lex->users_list))) my_ok(thd);
      break;
    }
    case SQLCOM_REVOKE:
    case SQLCOM_GRANT: {
      /* GRANT ... AS preliminery checks */
      if (lex->grant_as.grant_as_used) {
        if ((first_table || query_block->db)) {
          my_error(ER_UNSUPPORTED_USE_OF_GRANT_AS, MYF(0));
          goto error;
        }
      }
      /*
        Skip access check if we're granting a proxy
      */
      if (lex->type != TYPE_ENUM_PROXY) {
        /*
          If there are static grants in the GRANT statement or there are no
          dynamic privileges we perform check_access on GRANT_OPTION based on
          static global privilege level and set the DA accordingly.
        */
        if (lex->grant > 0 || lex->dynamic_privileges.elements == 0) {
          /*
            check_access sets DA error message based on GRANT arguments.
          */
          if (check_access(
                  thd, lex->grant | lex->grant_tot_col | GRANT_ACL,
                  first_table ? first_table->db : query_block->db,
                  first_table ? &first_table->grant.privilege : nullptr,
                  first_table ? &first_table->grant.m_internal : nullptr,
                  first_table ? false : true, false)) {
            goto error;
          }
        }
        /*
          ..else we still call check_access to load internal structures, but
          defer checking of global dynamic GRANT_OPTION to mysql_grant. We still
          ignore checks if this was a grant of a proxy.
        */
        else {
          /*
            check_access will load grant.privilege and grant.m_internal with
            values which are used later during column privilege checking. The
            return value isn't interesting as we'll check for dynamic global
            privileges later.
          */
          check_access(thd, lex->grant | lex->grant_tot_col | GRANT_ACL,
                       first_table ? first_table->db : query_block->db,
                       first_table ? &first_table->grant.privilege : nullptr,
                       first_table ? &first_table->grant.m_internal : nullptr,
                       first_table ? false : true, true);
        }
      }

      /* Replicate current user as grantor */
      thd->binlog_invoker();

      if (thd->security_context()->user().str)  // If not replication
      {
        LEX_USER *user, *tmp_user;
        bool first_user = true;

        List_iterator<LEX_USER> user_list(lex->users_list);
        while ((tmp_user = user_list++)) {
          if (!(user = get_current_user(thd, tmp_user))) goto error;
          if (specialflag & SPECIAL_NO_RESOLVE &&
              hostname_requires_resolving(user->host.str))
            push_warning(thd, Sql_condition::SL_WARNING,
                         ER_WARN_HOSTNAME_WONT_WORK,
                         ER_THD(thd, ER_WARN_HOSTNAME_WONT_WORK));
          // Are we trying to change a password of another user
          assert(user->host.str != nullptr);

          /*
            GRANT/REVOKE PROXY has the target user as a first entry in the list.
           */
          if (lex->type == TYPE_ENUM_PROXY && first_user) {
            first_user = false;
            if (acl_check_proxy_grant_access(thd, user->host.str,
                                             user->user.str,
                                             lex->grant & GRANT_ACL))
              goto error;
          }
        }
      }
      if (first_table) {
        if (lex->dynamic_privileges.elements > 0) {
          my_error(ER_ILLEGAL_PRIVILEGE_LEVEL, MYF(0), all_tables->table_name);
          goto error;
        }
        if (lex->type == TYPE_ENUM_PROCEDURE ||
            lex->type == TYPE_ENUM_FUNCTION) {
          uint grants = lex->all_privileges
                            ? (PROC_OP_ACLS) | (lex->grant & GRANT_ACL)
                            : lex->grant;
          if (check_grant_routine(thd, grants | GRANT_ACL, all_tables,
                                  lex->type == TYPE_ENUM_PROCEDURE, false))
            goto error;
          /* Conditionally writes to binlog */
          res = mysql_routine_grant(
              thd, all_tables, lex->type == TYPE_ENUM_PROCEDURE,
              lex->users_list, grants, lex->sql_command == SQLCOM_REVOKE, true);
          if (!res) my_ok(thd);
        } else {
          if (check_grant(thd, (lex->grant | lex->grant_tot_col | GRANT_ACL),
                          all_tables, false, UINT_MAX, false))
            goto error;
          /* Conditionally writes to binlog */
          res =
              mysql_table_grant(thd, all_tables, lex->users_list, lex->columns,
                                lex->grant, lex->sql_command == SQLCOM_REVOKE);
        }
      } else {
        if (lex->columns.elements ||
            (lex->type && lex->type != TYPE_ENUM_PROXY)) {
          my_error(ER_ILLEGAL_GRANT_FOR_TABLE, MYF(0));
          goto error;
        } else {
          /* Conditionally writes to binlog */
          res = mysql_grant(
              thd, query_block->db, lex->users_list, lex->grant,
              lex->sql_command == SQLCOM_REVOKE, lex->type == TYPE_ENUM_PROXY,
              lex->dynamic_privileges, lex->all_privileges, &lex->grant_as);
        }
      }
      break;
    }
    case SQLCOM_RESET:
      /*
        RESET commands are never written to the binary log, so we have to
        initialize this variable because RESET shares the same code as FLUSH
      */
      lex->no_write_to_binlog = true;
      if ((lex->type & REFRESH_PERSIST) && (lex->option_type == OPT_PERSIST)) {
        Persisted_variables_cache *pv =
            Persisted_variables_cache::get_instance();
        if (pv)
          if (pv->reset_persisted_variables(thd, lex->name.str,
                                            lex->drop_if_exists))
            goto error;
        my_ok(thd);
        break;
      }
      // Fall through.
    case SQLCOM_FLUSH: {
      int write_to_binlog;
      if (is_reload_request_denied(thd, lex->type)) goto error;

      if (first_table && lex->type & REFRESH_READ_LOCK) {
        /* Check table-level privileges. */
        if (check_table_access(thd, LOCK_TABLES_ACL | SELECT_ACL, all_tables,
                               false, UINT_MAX, false))
          goto error;
        if (flush_tables_with_read_lock(thd, all_tables)) goto error;
        my_ok(thd);
        break;
      } else if (first_table && lex->type & REFRESH_FOR_EXPORT) {
        /* Check table-level privileges. */
        if (check_table_access(thd, LOCK_TABLES_ACL | SELECT_ACL, all_tables,
                               false, UINT_MAX, false))
          goto error;
        if (flush_tables_for_export(thd, all_tables)) goto error;
        my_ok(thd);
        break;
      }

      /*
        handle_reload_request() will tell us if we are allowed to write to the
        binlog or not.
      */
      if (!handle_reload_request(thd, lex->type, first_table,
                                 &write_to_binlog)) {
        /*
          We WANT to write and we CAN write.
          ! we write after unlocking the table.
        */
        /*
          Presumably, RESET and binlog writing doesn't require synchronization
        */

        if (write_to_binlog > 0)  // we should write
        {
          if (!lex->no_write_to_binlog)
            res = write_bin_log(thd, false, thd->query().str,
                                thd->query().length);
        } else if (write_to_binlog < 0) {
          /*
             We should not write, but rather report error because
             handle_reload_request binlog interactions failed
           */
          res = 1;
        }

        if (!res) my_ok(thd);
      }

      break;
    }
    case SQLCOM_KILL: {
      Item *it = lex->kill_value_list.head();

      if (lex->table_or_sp_used()) {
        my_error(ER_NOT_SUPPORTED_YET, MYF(0),
                 "Usage of subqueries or stored "
                 "function calls as part of this statement");
        goto error;
      }

      if ((!it->fixed && it->fix_fields(lex->thd, &it)) || it->check_cols(1)) {
        my_error(ER_SET_CONSTANTS_ONLY, MYF(0));
        goto error;
      }

      my_thread_id thread_id = static_cast<my_thread_id>(it->val_int());
      if (thd->is_error()) goto error;

      sql_kill(thd, thread_id, lex->type & ONLY_KILL_QUERY);
      break;
    }
    case SQLCOM_SHOW_CREATE_USER: {
      LEX_USER *show_user = get_current_user(thd, lex->grant_user);
      Security_context *sctx = thd->security_context();
      bool are_both_users_same =
          !strcmp(sctx->priv_user().str, show_user->user.str) &&
          !my_strcasecmp(system_charset_info, show_user->host.str,
                         sctx->priv_host().str);
      if (are_both_users_same || !check_access(thd, SELECT_ACL, "mysql",
                                               nullptr, nullptr, true, false))
        res = mysql_show_create_user(thd, show_user, are_both_users_same);
      break;
    }
    case SQLCOM_BEGIN:
      if (trans_begin(thd, lex->start_transaction_opt)) goto error;
      my_ok(thd);
      break;
    case SQLCOM_COMMIT: {
      assert(thd->lock == nullptr ||
             thd->locked_tables_mode == LTM_LOCK_TABLES);
      bool tx_chain =
          (lex->tx_chain == TVL_YES ||
           (thd->variables.completion_type == 1 && lex->tx_chain != TVL_NO));
      bool tx_release =
          (lex->tx_release == TVL_YES ||
           (thd->variables.completion_type == 2 && lex->tx_release != TVL_NO));
      if (trans_commit(thd)) goto error;
      thd->mdl_context.release_transactional_locks();
      /* Begin transaction with the same isolation level. */
      if (tx_chain) {
        if (trans_begin(thd)) goto error;
      } else {
        /* Reset the isolation level and access mode if no chaining
         * transaction.*/
        trans_reset_one_shot_chistics(thd);
      }
      /* Disconnect the current client connection. */
      if (tx_release) thd->killed = THD::KILL_CONNECTION;
      my_ok(thd);
      break;
    }
    case SQLCOM_ROLLBACK: {
      assert(thd->lock == nullptr ||
             thd->locked_tables_mode == LTM_LOCK_TABLES);
      bool tx_chain =
          (lex->tx_chain == TVL_YES ||
           (thd->variables.completion_type == 1 && lex->tx_chain != TVL_NO));
      bool tx_release =
          (lex->tx_release == TVL_YES ||
           (thd->variables.completion_type == 2 && lex->tx_release != TVL_NO));
      if (trans_rollback(thd)) goto error;
      thd->mdl_context.release_transactional_locks();
      /* Begin transaction with the same isolation level. */
      if (tx_chain) {
        if (trans_begin(thd)) goto error;
      } else {
        /* Reset the isolation level and access mode if no chaining
         * transaction.*/
        trans_reset_one_shot_chistics(thd);
      }
      /* Disconnect the current client connection. */
      if (tx_release) thd->killed = THD::KILL_CONNECTION;
      my_ok(thd);
      break;
    }
    case SQLCOM_RELEASE_SAVEPOINT:
      if (trans_release_savepoint(thd, lex->ident)) goto error;
      my_ok(thd);
      break;
    case SQLCOM_ROLLBACK_TO_SAVEPOINT:
      if (trans_rollback_to_savepoint(thd, lex->ident)) goto error;
      my_ok(thd);
      break;
    case SQLCOM_SAVEPOINT:
      if (trans_savepoint(thd, lex->ident)) goto error;
      my_ok(thd);
      break;
    case SQLCOM_CREATE_PROCEDURE:
    case SQLCOM_CREATE_SPFUNCTION: {
      uint namelen;
      char *name;

      assert(lex->sphead != nullptr);
      assert(lex->sphead->m_db.str); /* Must be initialized in the parser */
      /*
        Verify that the database name is allowed, optionally
        lowercase it.
      */
      if (check_and_convert_db_name(&lex->sphead->m_db, false) !=
          Ident_name_check::OK)
        goto error;

      if (check_access(thd, CREATE_PROC_ACL, lex->sphead->m_db.str, nullptr,
                       nullptr, false, false))
        goto error;

      name = lex->sphead->name(&namelen);
      if (lex->sphead->m_type == enum_sp_type::FUNCTION) {
        udf_func *udf = find_udf(name, namelen);

        if (udf) {
          my_error(ER_UDF_EXISTS, MYF(0), name);
          goto error;
        }
      }

      if (sp_process_definer(thd)) goto error;

      /*
        Record the CURRENT_USER in binlog. The CURRENT_USER is used on slave to
        grant default privileges when sp_automatic_privileges variable is set.
      */
      thd->binlog_invoker();

      if (!(res = sp_create_routine(thd, lex->sphead, thd->lex->definer))) {
        /* only add privileges if really neccessary */

        Security_context security_context;
        bool restore_backup_context = false;
        Security_context *backup = nullptr;
        /*
          We're going to issue an implicit GRANT statement so we close all
          open tables. We have to keep metadata locks as this ensures that
          this statement is atomic against concurent FLUSH TABLES WITH READ
          LOCK. Deadlocks which can arise due to fact that this implicit
          statement takes metadata locks should be detected by a deadlock
          detector in MDL subsystem and reported as errors.

          No need to commit/rollback statement transaction, it's not started.

          TODO: Long-term we should either ensure that implicit GRANT statement
                is written into binary log as a separate statement or make both
                creation of routine and implicit GRANT parts of one fully atomic
                statement.
        */
        assert(thd->get_transaction()->is_empty(Transaction_ctx::STMT));
        close_thread_tables(thd);
        /*
          Check if invoker exists on slave, then use invoker privilege to
          insert routine privileges to mysql.procs_priv. If invoker is not
          available then consider using definer.

          Check if the definer exists on slave,
          then use definer privilege to insert routine privileges to
          mysql.procs_priv.

          For current user of SQL thread has GLOBAL_ACL privilege,
          which doesn't any check routine privileges,
          so no routine privilege record  will insert into mysql.procs_priv.
        */

        if (thd->slave_thread) {
          LEX_CSTRING current_user;
          LEX_CSTRING current_host;
          if (thd->has_invoker()) {
            current_host = thd->get_invoker_host();
            current_user = thd->get_invoker_user();
          } else {
            current_host = lex->definer->host;
            current_user = lex->definer->user;
          }
          if (is_acl_user(thd, current_host.str, current_user.str)) {
            security_context.change_security_context(
                thd, current_user, current_host, thd->lex->sphead->m_db.str,
                &backup);
            restore_backup_context = true;
          }
        }

        if (sp_automatic_privileges && !opt_noacl &&
            check_routine_access(
                thd, DEFAULT_CREATE_PROC_ACLS, lex->sphead->m_db.str, name,
                lex->sql_command == SQLCOM_CREATE_PROCEDURE, true)) {
          if (sp_grant_privileges(thd, lex->sphead->m_db.str, name,
                                  lex->sql_command == SQLCOM_CREATE_PROCEDURE))
            push_warning(thd, Sql_condition::SL_WARNING,
                         ER_PROC_AUTO_GRANT_FAIL,
                         ER_THD(thd, ER_PROC_AUTO_GRANT_FAIL));
          thd->clear_error();
        }

        /*
          Restore current user with GLOBAL_ACL privilege of SQL thread
        */
        if (restore_backup_context) {
          assert(thd->slave_thread == 1);
          thd->security_context()->restore_security_context(thd, backup);
        }
        my_ok(thd);
      }
      break; /* break super switch */
    }        /* end case group bracket */

    case SQLCOM_ALTER_PROCEDURE:
    case SQLCOM_ALTER_FUNCTION: {
      if (check_routine_access(thd, ALTER_PROC_ACL, lex->spname->m_db.str,
                               lex->spname->m_name.str,
                               lex->sql_command == SQLCOM_ALTER_PROCEDURE,
                               false))
        goto error;

      enum_sp_type sp_type = (lex->sql_command == SQLCOM_ALTER_PROCEDURE)
                                 ? enum_sp_type::PROCEDURE
                                 : enum_sp_type::FUNCTION;
      /*
        Note that if you implement the capability of ALTER FUNCTION to
        alter the body of the function, this command should be made to
        follow the restrictions that log-bin-trust-function-creators=0
        already puts on CREATE FUNCTION.
      */
      /* Conditionally writes to binlog */
      res = sp_update_routine(thd, sp_type, lex->spname, &lex->sp_chistics);
      if (res || thd->killed) goto error;

      my_ok(thd);
      break;
    }
    case SQLCOM_DROP_PROCEDURE:
    case SQLCOM_DROP_FUNCTION: {
      if (lex->sql_command == SQLCOM_DROP_FUNCTION &&
          !lex->spname->m_explicit_name) {
        /* DROP FUNCTION <non qualified name> */
        udf_func *udf =
            find_udf(lex->spname->m_name.str, lex->spname->m_name.length);
        if (udf) {
          if (check_access(thd, DELETE_ACL, "mysql", nullptr, nullptr, true,
                           false))
            goto error;

          if (!(res = mysql_drop_function(thd, &lex->spname->m_name))) {
            my_ok(thd);
            break;
          }
          my_error(ER_SP_DROP_FAILED, MYF(0), "FUNCTION (UDF)",
                   lex->spname->m_name.str);
          goto error;
        }

        if (lex->spname->m_db.str == nullptr) {
          if (lex->drop_if_exists) {
            push_warning_printf(thd, Sql_condition::SL_NOTE,
                                ER_SP_DOES_NOT_EXIST,
                                ER_THD(thd, ER_SP_DOES_NOT_EXIST),
                                "FUNCTION (UDF)", lex->spname->m_name.str);
            res = false;
            my_ok(thd);
            break;
          }
          my_error(ER_SP_DOES_NOT_EXIST, MYF(0), "FUNCTION (UDF)",
                   lex->spname->m_name.str);
          goto error;
        }
        /* Fall thought to test for a stored function */
      }

      const char *db = lex->spname->m_db.str;
      char *name = lex->spname->m_name.str;

      if (check_routine_access(thd, ALTER_PROC_ACL, db, name,
                               lex->sql_command == SQLCOM_DROP_PROCEDURE,
                               false))
        goto error;

      enum_sp_type sp_type = (lex->sql_command == SQLCOM_DROP_PROCEDURE)
                                 ? enum_sp_type::PROCEDURE
                                 : enum_sp_type::FUNCTION;

      /* Conditionally writes to binlog */
      enum_sp_return_code sp_result =
          sp_drop_routine(thd, sp_type, lex->spname);

      /*
        We're going to issue an implicit REVOKE statement so we close all
        open tables. We have to keep metadata locks as this ensures that
        this statement is atomic against concurent FLUSH TABLES WITH READ
        LOCK. Deadlocks which can arise due to fact that this implicit
        statement takes metadata locks should be detected by a deadlock
        detector in MDL subsystem and reported as errors.

        No need to commit/rollback statement transaction, it's not started.

        TODO: Long-term we should either ensure that implicit REVOKE statement
              is written into binary log as a separate statement or make both
              dropping of routine and implicit REVOKE parts of one fully atomic
              statement.
      */
      assert(thd->get_transaction()->is_empty(Transaction_ctx::STMT));
      close_thread_tables(thd);

      if (sp_result != SP_DOES_NOT_EXISTS && sp_automatic_privileges &&
          !opt_noacl &&
          sp_revoke_privileges(thd, db, name,
                               lex->sql_command == SQLCOM_DROP_PROCEDURE)) {
        push_warning(thd, Sql_condition::SL_WARNING, ER_PROC_AUTO_REVOKE_FAIL,
                     ER_THD(thd, ER_PROC_AUTO_REVOKE_FAIL));
        /* If this happens, an error should have been reported. */
        goto error;
      }

      res = sp_result;
      switch (sp_result) {
        case SP_OK:
          my_ok(thd);
          break;
        case SP_DOES_NOT_EXISTS:
          if (lex->drop_if_exists) {
            res =
                write_bin_log(thd, true, thd->query().str, thd->query().length);
            push_warning_printf(thd, Sql_condition::SL_NOTE,
                                ER_SP_DOES_NOT_EXIST,
                                ER_THD(thd, ER_SP_DOES_NOT_EXIST),
                                SP_COM_STRING(lex), lex->spname->m_qname.str);
            if (!res) my_ok(thd);
            break;
          }
          my_error(ER_SP_DOES_NOT_EXIST, MYF(0), SP_COM_STRING(lex),
                   lex->spname->m_qname.str);
          goto error;
        default:
          my_error(ER_SP_DROP_FAILED, MYF(0), SP_COM_STRING(lex),
                   lex->spname->m_qname.str);
          goto error;
      }
      break;
    }
    case SQLCOM_CREATE_VIEW: {
      /*
        Note: SQLCOM_CREATE_VIEW also handles 'ALTER VIEW' commands
        as specified through the thd->lex->create_view_mode flag.
      */
      res = mysql_create_view(thd, first_table, thd->lex->create_view_mode);
      break;
    }
    case SQLCOM_DROP_VIEW: {
      if (check_table_access(thd, DROP_ACL, all_tables, false, UINT_MAX, false))
        goto error;
      /* Conditionally writes to binlog. */
      res = mysql_drop_view(thd, first_table);
      break;
    }
    case SQLCOM_CREATE_TRIGGER:
    case SQLCOM_DROP_TRIGGER: {
      /* Conditionally writes to binlog. */
      assert(lex->m_sql_cmd != nullptr);
      static_cast<Sql_cmd_ddl_trigger_common *>(lex->m_sql_cmd)
          ->set_table(all_tables);

      res = lex->m_sql_cmd->execute(thd);
      break;
    }
    case SQLCOM_BINLOG_BASE64_EVENT: {
      mysql_client_binlog_statement(thd);
      break;
    }
    case SQLCOM_ANALYZE:
    case SQLCOM_CHECK:
    case SQLCOM_OPTIMIZE:
    case SQLCOM_REPAIR:
    case SQLCOM_TRUNCATE:
    case SQLCOM_ALTER_TABLE:
    case SQLCOM_HA_OPEN:
    case SQLCOM_HA_READ:
    case SQLCOM_HA_CLOSE:
      assert(first_table == all_tables && first_table != nullptr);
      /* fall through */
    case SQLCOM_CREATE_SERVER:
    case SQLCOM_CREATE_RESOURCE_GROUP:
    case SQLCOM_ALTER_SERVER:
    case SQLCOM_ALTER_RESOURCE_GROUP:
    case SQLCOM_DROP_RESOURCE_GROUP:
    case SQLCOM_DROP_SERVER:
    case SQLCOM_SET_RESOURCE_GROUP:
    case SQLCOM_SIGNAL:
    case SQLCOM_RESIGNAL:
    case SQLCOM_GET_DIAGNOSTICS:
    case SQLCOM_CHANGE_REPLICATION_FILTER:
    case SQLCOM_XA_START:
    case SQLCOM_XA_END:
    case SQLCOM_XA_PREPARE:
    case SQLCOM_XA_COMMIT:
    case SQLCOM_XA_ROLLBACK:
    case SQLCOM_XA_RECOVER:
    case SQLCOM_INSTALL_PLUGIN:
    case SQLCOM_UNINSTALL_PLUGIN:
    case SQLCOM_INSTALL_COMPONENT:
    case SQLCOM_UNINSTALL_COMPONENT:
    case SQLCOM_SHUTDOWN:
    case SQLCOM_ALTER_INSTANCE:
    case SQLCOM_SELECT:
    case SQLCOM_DO:
    case SQLCOM_CALL:
    case SQLCOM_CREATE_ROLE:
    case SQLCOM_DROP_ROLE:
    case SQLCOM_SET_ROLE:
    case SQLCOM_GRANT_ROLE:
    case SQLCOM_REVOKE_ROLE:
    case SQLCOM_ALTER_USER_DEFAULT_ROLE:
    case SQLCOM_SHOW_BINLOG_EVENTS:
    case SQLCOM_SHOW_BINLOGS:
    case SQLCOM_SHOW_CHARSETS:
    case SQLCOM_SHOW_COLLATIONS:
    case SQLCOM_SHOW_CREATE_DB:
    case SQLCOM_SHOW_CREATE_EVENT:
    case SQLCOM_SHOW_CREATE_FUNC:
    case SQLCOM_SHOW_CREATE_PROC:
    case SQLCOM_SHOW_CREATE:
    case SQLCOM_SHOW_CREATE_TRIGGER:
    // case SQLCOM_SHOW_CREATE_USER:
    case SQLCOM_SHOW_DATABASES:
    case SQLCOM_SHOW_ENGINE_LOGS:
    case SQLCOM_SHOW_ENGINE_MUTEX:
    case SQLCOM_SHOW_ENGINE_STATUS:
    case SQLCOM_SHOW_ERRORS:
    case SQLCOM_SHOW_EVENTS:
    case SQLCOM_SHOW_FIELDS:
    case SQLCOM_SHOW_FUNC_CODE:
    case SQLCOM_SHOW_GRANTS:
    case SQLCOM_SHOW_KEYS:
    case SQLCOM_SHOW_MASTER_STAT:
    case SQLCOM_SHOW_OPEN_TABLES:
    case SQLCOM_SHOW_PLUGINS:
    case SQLCOM_SHOW_PRIVILEGES:
    case SQLCOM_SHOW_PROC_CODE:
    case SQLCOM_SHOW_PROCESSLIST:
    case SQLCOM_SHOW_PROFILE:
    case SQLCOM_SHOW_PROFILES:
    case SQLCOM_SHOW_RELAYLOG_EVENTS:
    case SQLCOM_SHOW_SLAVE_HOSTS:
    case SQLCOM_SHOW_SLAVE_STAT:
    case SQLCOM_SHOW_STATUS:
    case SQLCOM_SHOW_STORAGE_ENGINES:
    case SQLCOM_SHOW_TABLE_STATUS:
    case SQLCOM_SHOW_TABLES:
    case SQLCOM_SHOW_TRIGGERS:
    case SQLCOM_SHOW_STATUS_PROC:
    case SQLCOM_SHOW_STATUS_FUNC:
    case SQLCOM_SHOW_VARIABLES:
    case SQLCOM_SHOW_WARNS:
    case SQLCOM_CLONE:
    case SQLCOM_LOCK_INSTANCE:
    case SQLCOM_UNLOCK_INSTANCE:
    case SQLCOM_ALTER_TABLESPACE:
    case SQLCOM_EXPLAIN_OTHER:
    case SQLCOM_RESTART_SERVER:
    case SQLCOM_CREATE_SRS:
    case SQLCOM_DROP_SRS: {
      assert(lex->m_sql_cmd != nullptr);

      res = lex->m_sql_cmd->execute(thd);

      break;
    }
    case SQLCOM_ALTER_USER: {
      LEX_USER *user, *tmp_user;
      bool changing_own_password = false;
      Security_context *sctx = thd->security_context();
      bool own_password_expired = sctx->password_expired();
      bool check_permission = true;

      List_iterator<LEX_USER> user_list(lex->users_list);
      while ((tmp_user = user_list++)) {
        bool update_password_only = false;
        bool is_self = false;
        bool second_password = false;

        /* If it is an empty lex_user update it with current user */
        if (!tmp_user->host.str && !tmp_user->user.str) {
          /* set user information as of the current user */
          assert(sctx->priv_host().str);
          tmp_user->host.str = sctx->priv_host().str;
          tmp_user->host.length = strlen(sctx->priv_host().str);
          assert(sctx->user().str);
          tmp_user->user.str = sctx->user().str;
          tmp_user->user.length = strlen(sctx->user().str);
        }
        if (!(user = get_current_user(thd, tmp_user))) goto error;

        /* copy password expire attributes to individual lex user */
        user->alter_status = thd->lex->alter_password;

        /*
          Only self password change is non-privileged operation. To detect the
          same, we find :
          (1) If it is only password change operation
          (2) If this operation is on self
        */
        if (user->uses_identified_by_clause &&
            !user->uses_identified_with_clause &&
            !thd->lex->mqh.specified_limits &&
            !user->alter_status.update_account_locked_column &&
            !user->alter_status.update_password_expired_column &&
            !user->alter_status.expire_after_days &&
            user->alter_status.use_default_password_lifetime &&
            (user->alter_status.update_password_require_current ==
             Lex_acl_attrib_udyn::UNCHANGED) &&
            !user->alter_status.update_password_history &&
            !user->alter_status.update_password_reuse_interval &&
            !user->alter_status.update_failed_login_attempts &&
            !user->alter_status.update_password_lock_time &&
            (thd->lex->ssl_type == SSL_TYPE_NOT_SPECIFIED))
          update_password_only = true;

        is_self = !strcmp(sctx->user().length ? sctx->user().str : "",
                          user->user.str) &&
                  !my_strcasecmp(&my_charset_latin1, user->host.str,
                                 sctx->priv_host().str);
        /*
          if user executes ALTER statement to change password only
          for himself then skip access check - Provided preference to
          retain/discard current password was specified.
        */

        if (user->discard_old_password || user->retain_current_password) {
          second_password = true;
        }
        if ((update_password_only || user->discard_old_password) && is_self) {
          changing_own_password = update_password_only;
          if (second_password) {
            if (check_access(thd, UPDATE_ACL, consts::mysql.c_str(), nullptr,
                             nullptr, true, true) &&
                !sctx->check_access(CREATE_USER_ACL, consts::mysql.c_str()) &&
                !(sctx->has_global_grant(
                          STRING_WITH_LEN("APPLICATION_PASSWORD_ADMIN"))
                      .first)) {
              my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0),
                       "CREATE USER or APPLICATION_PASSWORD_ADMIN");
              goto error;
            }
          }
          continue;
        } else if (check_permission) {
          if (check_access(thd, UPDATE_ACL, "mysql", nullptr, nullptr, true,
                           true) &&
              check_global_access(thd, CREATE_USER_ACL))
            goto error;

          check_permission = false;
        }

        if (is_self && (user->uses_identified_by_clause ||
                        user->uses_identified_with_clause ||
                        user->uses_authentication_string_clause)) {
          changing_own_password = true;
          break;
        }

        if (update_password_only &&
            likely((get_server_state() == SERVER_OPERATING)) &&
            !strcmp(sctx->priv_user().str, "")) {
          my_error(ER_PASSWORD_ANONYMOUS_USER, MYF(0));
          goto error;
        }
      }

      if (unlikely(own_password_expired && !changing_own_password)) {
        my_error(ER_MUST_CHANGE_PASSWORD, MYF(0));
        goto error;
      }

      /* Conditionally writes to binlog */
      res = mysql_alter_user(thd, lex->users_list, lex->drop_if_exists);
      break;
    }
    default:
      assert(0); /* Impossible */
      my_ok(thd);
      break;
  }
  goto finish;

error:
  res = true;

finish:
  /* Restore system variables which were changed by SET_VAR hint. */
  if (lex->opt_hints_global && lex->opt_hints_global->sys_var_hint)
    lex->opt_hints_global->sys_var_hint->restore_vars(thd);

  THD_STAGE_INFO(thd, stage_query_end);

  if (res) {
    if (thd->get_reprepare_observer() != nullptr &&
        thd->get_reprepare_observer()->is_invalidated() &&
        thd->get_reprepare_observer()->can_retry())
      thd->skip_gtid_rollback = true;
  } else {
    lex->set_exec_started();
  }

  // Cleanup EXPLAIN info
  if (!thd->in_sub_stmt) {
    if (is_explainable_query(lex->sql_command)) {
      DEBUG_SYNC(thd, "before_reset_query_plan");
      /*
        We want EXPLAIN CONNECTION to work until the explained statement ends,
        thus it is only now that we may fully clean up any unit of this
        statement.
      */
      lex->unit->assert_not_fully_clean();
    }
    thd->query_plan.set_query_plan(SQLCOM_END, nullptr, false);
  }

  assert(!thd->in_active_multi_stmt_transaction() ||
         thd->in_multi_stmt_transaction_mode());

  if (!thd->in_sub_stmt) {
    mysql_audit_notify(thd,
                       first_level ? MYSQL_AUDIT_QUERY_STATUS_END
                                   : MYSQL_AUDIT_QUERY_NESTED_STATUS_END,
                       first_level ? "MYSQL_AUDIT_QUERY_STATUS_END"
                                   : "MYSQL_AUDIT_QUERY_NESTED_STATUS_END");

    /* report error issued during command execution */
    if (thd->killed) thd->send_kill_message();
    if (thd->is_error() ||
        (thd->variables.option_bits & OPTION_MASTER_SQL_ERROR))
      trans_rollback_stmt(thd);
    else {
      /* If commit fails, we should be able to reset the OK status. */
      thd->get_stmt_da()->set_overwrite_status(true);
      trans_commit_stmt(thd);
      thd->get_stmt_da()->set_overwrite_status(false);
    }
    /*
      Reset thd killed flag during cleanup so that commands which are
      dispatched using service session API's start with a clean state.
    */
    if (thd->killed == THD::KILL_QUERY || thd->killed == THD::KILL_TIMEOUT) {
      thd->killed = THD::NOT_KILLED;
      thd->reset_query_for_display();
    }
  }

  lex->cleanup(thd, true);
  /* Free tables */
  THD_STAGE_INFO(thd, stage_closing_tables);
  close_thread_tables(thd);

  // Rollback any item transformations made during optimization and execution
  thd->rollback_item_tree_changes();

#ifndef NDEBUG
  if (lex->sql_command != SQLCOM_SET_OPTION && !thd->in_sub_stmt)
    DEBUG_SYNC(thd, "execute_command_after_close_tables");
#endif

  if (!thd->in_sub_stmt && thd->transaction_rollback_request) {
    /*
      We are not in sub-statement and transaction rollback was requested by
      one of storage engines (e.g. due to deadlock). Rollback transaction in
      all storage engines including binary log.
    */
    trans_rollback_implicit(thd);
    thd->mdl_context.release_transactional_locks();
  } else if (stmt_causes_implicit_commit(thd, CF_IMPLICIT_COMMIT_END)) {
    /* No transaction control allowed in sub-statements. */
    assert(!thd->in_sub_stmt);
    /* If commit fails, we should be able to reset the OK status. */
    thd->get_stmt_da()->set_overwrite_status(true);
    /* Commit the normal transaction if one is active. */
    trans_commit_implicit(thd);
    thd->get_stmt_da()->set_overwrite_status(false);
    thd->mdl_context.release_transactional_locks();
  } else if (!thd->in_sub_stmt && !thd->in_multi_stmt_transaction_mode()) {
    /*
      - If inside a multi-statement transaction,
      defer the release of metadata locks until the current
      transaction is either committed or rolled back. This prevents
      other statements from modifying the table for the entire
      duration of this transaction.  This provides commit ordering
      and guarantees serializability across multiple transactions.
      - If in autocommit mode, or outside a transactional context,
      automatically release metadata locks of the current statement.
    */
    thd->mdl_context.release_transactional_locks();
  } else if (!thd->in_sub_stmt &&
             (thd->lex->sql_command != SQLCOM_CREATE_TABLE ||
              !thd->lex->create_info->m_transactional_ddl)) {
    thd->mdl_context.release_statement_locks();
  }

  // If the client wishes to have transaction state reported, we add whatever
  // is set on THD to our set here.
  {
    TX_TRACKER_GET(tst);

    if (thd->variables.session_track_transaction_info > TX_TRACK_NONE)
      tst->add_trx_state_from_thd(thd);

    // We're done. Clear "is DML" flag.
    tst->clear_trx_state(thd, TX_STMT_DML);
  }

#ifdef HAVE_LSAN_DO_RECOVERABLE_LEAK_CHECK
  // Get incremental leak reports, for easier leak hunting.
  // ./mtr --mem --mysqld='-T 4096' --sanitize main.1st
  // Don't waste time calling leak sanitizer during bootstrap.
  if (!opt_initialize && (test_flags & TEST_DO_QUICK_LEAK_CHECK)) {
    int have_leaks = __lsan_do_recoverable_leak_check();
    if (have_leaks > 0) {
      fprintf(stderr, "LSAN found leaks for Query: %*s\n",
              static_cast<int>(thd->query().length), thd->query().str);
      fflush(stderr);
    }
  }
#endif

#if defined(VALGRIND_DO_QUICK_LEAK_CHECK)
  // Get incremental leak reports, for easier leak hunting.
  // ./mtr --mem --mysqld='-T 4096' --valgrind-mysqld main.1st
  // Note that with multiple connections, the report below may be misleading.
  if (test_flags & TEST_DO_QUICK_LEAK_CHECK) {
    static unsigned long total_leaked_bytes = 0;
    unsigned long leaked = 0;
    unsigned long dubious MY_ATTRIBUTE((unused));
    unsigned long reachable MY_ATTRIBUTE((unused));
    unsigned long suppressed MY_ATTRIBUTE((unused));
    /*
      We could possibly use VALGRIND_DO_CHANGED_LEAK_CHECK here,
      but that is a fairly new addition to the Valgrind api.
      Note: we dont want to check 'reachable' until we have done shutdown,
      and that is handled by the final report anyways.
      We print some extra information, to tell mtr to ignore this report.
    */
    LogErr(INFORMATION_LEVEL, ER_VALGRIND_DO_QUICK_LEAK_CHECK);
    VALGRIND_DO_QUICK_LEAK_CHECK;
    VALGRIND_COUNT_LEAKS(leaked, dubious, reachable, suppressed);
    if (leaked > total_leaked_bytes) {
      LogErr(ERROR_LEVEL, ER_VALGRIND_COUNT_LEAKS, leaked - total_leaked_bytes,
             static_cast<int>(thd->query().length), thd->query().str);
    }
    total_leaked_bytes = leaked;
  }
#endif

  if (!res && !thd->is_error()) {      // if statement succeeded
    binlog_gtid_end_transaction(thd);  // finalize GTID life-cycle
    DEBUG_SYNC(thd, "persist_new_state_after_statement_succeeded");
  } else if (!gtid_consistency_violation_state &&    // if the consistency state
             thd->has_gtid_consistency_violation) {  // was set by the failing
                                                     // statement
    gtid_state->end_gtid_violating_transaction(thd);  // just roll it back
    DEBUG_SYNC(thd, "restore_previous_state_after_statement_failed");
  }

  thd->skip_gtid_rollback = false;

  return res || thd->is_error();
}

```
