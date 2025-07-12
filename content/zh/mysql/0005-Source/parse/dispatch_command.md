---
title: dispatch_command
description: dispatch_command
date: 2023-10-30
weight: 10001


---

{{< alert >}}
8.0.32

**负责处理客户端发送的命令：**  总的来说，dispatch_command 函数是 MySQL 服务器中用于处理客户端命令的重要函数，它负责接收客户端发送的命令，并根据命令类型执行相应的业务逻辑，最终返回命令执行是否出错的结果。

参数说明：
- thd：当前会话的线程处理器（Thread Handle），用于管理当前客户端连接的状态和数据。
- com_data：客户端发送的命令数据。
- command：表示客户端请求的命令类型。
- 返回值：布尔值，表示命令执行是否出错。

{{< /alert >}}




- COM_INIT_DB 切换数据库
- COM_REGISTER_SLAVE：注册从服务器。
- COM_RESET_CONNECTION：重置连接状态。
- COM_CLONE：执行克隆命令。
- COM_CHANGE_USER：更改当前用户。
- COM_STMT_EXECUTE、COM_STMT_FETCH等：处理预处理语句相关命令。
- COM_QUERY：处理普通 SQL 查询。
- COM_QUIT：关闭连接。
- COM_BINLOG_DUMP_GTID、COM_BINLOG_DUMP：处理二进制日志相关命令。
- COM_REFRESH、COM_STATISTICS、COM_PING等：处理刷新、统计、Ping等命令。


### dispatch_command
```c++
bool dispatch_command(THD *thd, const COM_DATA *com_data, enum enum_server_command command) {
        assert(thd->lex->m_IS_table_stats.is_valid() == false);
        assert(thd->lex->m_IS_tablespace_stats.is_valid() == false);
#ifndef NDEBUG
        auto tabstat_grd = create_scope_guard([&]() {
                assert(thd->lex->m_IS_table_stats.is_valid() == false);
                assert(thd->lex->m_IS_tablespace_stats.is_valid() == false);
        });
#endif /* NDEBUG */
        bool                error       = false;
        Global_THD_manager *thd_manager = Global_THD_manager::get_instance();
        DBUG_TRACE;
        DBUG_PRINT("info", ("command: %d", command));

        Sql_cmd_clone *clone_cmd = nullptr;

        /* SHOW PROFILE instrumentation, begin */
#if defined(ENABLED_PROFILING)
        thd->profiling->start_new_query();
#endif

        /* Performance Schema Interface instrumentation, begin */
        thd->m_statement_psi = MYSQL_REFINE_STATEMENT(thd->m_statement_psi, com_statement_info[command].m_key);

        thd->set_command(command);
        /*
          Commands which always take a long time are logged into
          the slow log only if opt_log_slow_admin_statements is set.
        */
        thd->enable_slow_log  = true;
        thd->lex->sql_command = SQLCOM_END; /* to avoid confusing VIEW detectors */
        /*
          KILL QUERY may come after cleanup in mysql_execute_command(). Next
          query execution is interrupted due to this. So resetting THD::killed
          here.

          THD::killed value can not be KILL_TIMEOUT here as timer used for
          statement max execution time is disarmed in the cleanup stage of
          mysql_execute_command. KILL CONNECTION should terminate the
          connection. Hence resetting THD::killed only for KILL QUERY case here.
        */
        if (thd->killed == THD::KILL_QUERY)
                thd->killed = THD::NOT_KILLED;
        thd->set_time();
        if (is_time_t_valid_for_timestamp(thd->query_start_in_secs()) == false) {
                /*
                  If the time has gone past end of epoch we need to shutdown the
                  server. But there is possibility of getting invalid time value
                  on some platforms. For example, gettimeofday() might return
                  incorrect value on solaris platform. Hence validating the
                  current time with 5 iterations before initiating the normal
                  server shutdown process because of time getting past 2038.
                */
                const int max_tries = 5;
                LogErr(WARNING_LEVEL, ER_CONFIRMING_THE_FUTURE, max_tries);

                int tries = 0;
                while (++tries <= max_tries) {
                        thd->set_time();
                        if (is_time_t_valid_for_timestamp(thd->query_start_in_secs()) == true) {
                                LogErr(WARNING_LEVEL, ER_BACK_IN_TIME, tries);
                                break;
                        }
                        LogErr(WARNING_LEVEL, ER_FUTURE_DATE, tries);
                }
                if (tries > max_tries) {
                        /*
                          If the time has got past epoch, we need to shut this
                          server down. We do this by making sure every command
                          is a shutdown and we have enough privileges to shut
                          the server down

                          TODO: remove this when we have full 64 bit my_time_t
                          support
                        */
                        LogErr(ERROR_LEVEL, ER_UNSUPPORTED_DATE);
                        ulong master_access = thd->security_context()->master_access();
                        thd->security_context()->set_master_access(master_access | SHUTDOWN_ACL);
                        error = true;
                        kill_mysql();
                }
        }
        thd->set_query_id(next_query_id());
        thd->reset_rewritten_query();
        thd_manager->inc_thread_running();

        if (!(server_command_flags[command] & CF_SKIP_QUESTIONS))
                thd->status_var.questions++;

        /**
          Clear the set of flags that are expected to be cleared at the
          beginning of each command.
        */
        thd->server_status &= ~SERVER_STATUS_CLEAR_SET;

        if (thd->get_protocol()->type() == Protocol::PROTOCOL_PLUGIN &&
            !(server_command_flags[command] & CF_ALLOW_PROTOCOL_PLUGIN)) {
                my_error(ER_PLUGGABLE_PROTOCOL_COMMAND_NOT_SUPPORTED, MYF(0));
                thd->killed = THD::KILL_CONNECTION;
                error       = true;
                goto done;
        }

        /**
          Enforce password expiration for all RPC commands, except the
          following:

          COM_QUERY/COM_STMT_PREPARE and COM_STMT_EXECUTE do a more
          fine-grained check later.
          COM_STMT_CLOSE and COM_STMT_SEND_LONG_DATA don't return anything.
          COM_PING only discloses information that the server is running,
             and that's available through other means.
          COM_QUIT should work even for expired statements.
        */
        if (unlikely(thd->security_context()->password_expired() && command != COM_QUERY && command != COM_STMT_CLOSE &&
                     command != COM_STMT_SEND_LONG_DATA && command != COM_PING && command != COM_QUIT &&
                     command != COM_STMT_PREPARE && command != COM_STMT_EXECUTE)) {
                my_error(ER_MUST_CHANGE_PASSWORD, MYF(0));
                goto done;
        }

        if (mysql_audit_notify(
                    thd, AUDIT_EVENT(MYSQL_AUDIT_COMMAND_START), command, Command_names::str_global(command).c_str())) {
                goto done;
        }

        switch (command) {
        case COM_INIT_DB: {
                LEX_STRING tmp;
                thd->status_var.com_stat[SQLCOM_CHANGE_DB]++;
                thd->convert_string(
                        &tmp, system_charset_info, com_data->com_init_db.db_name, com_data->com_init_db.length, thd->charset());

                LEX_CSTRING tmp_cstr = { tmp.str, tmp.length };
                if (!mysql_change_db(thd, tmp_cstr, false)) {
                        query_logger.general_log_write(thd, command, thd->db().str, thd->db().length);
                        my_ok(thd);
                }
                break;
        }
        case COM_REGISTER_SLAVE: {
                // TODO: access of protocol_classic should be removed
                if (!register_replica(thd,
                                      thd->get_protocol_classic()->get_raw_packet(),
                                      thd->get_protocol_classic()->get_packet_length()))
                        my_ok(thd);
                break;
        }
        case COM_RESET_CONNECTION: {
                thd->status_var.com_other++;
                thd->cleanup_connection();
                my_ok(thd);
                break;
        }
        case COM_CLONE: {
                thd->status_var.com_other++;

                /* Try loading clone plugin */
                clone_cmd = new (thd->mem_root) Sql_cmd_clone();

                if (clone_cmd && clone_cmd->load(thd)) {
                        clone_cmd = nullptr;
                }

                thd->lex->m_sql_cmd   = clone_cmd;
                thd->lex->sql_command = SQLCOM_CLONE;

                break;
        }
        case COM_SUBSCRIBE_GROUP_REPLICATION_STREAM: {
                Security_context *sctx = thd->security_context();
                if (!sctx->has_global_grant(STRING_WITH_LEN("GROUP_REPLICATION_STREAM")).first) {
                        my_error(ER_SPECIFIC_ACCESS_DENIED_ERROR, MYF(0), "");
                        error = true;
                        break;
                }

                if (!error && get_gr_incoming_connection() == nullptr) {
                        my_error(ER_UNKNOWN_COM_ERROR, MYF(0));
                        error = true;
                        break;
                }

                my_ok(thd);

                break;
        }
        case COM_CHANGE_USER: {
                /*
                  LOCK_thd_security_ctx protects the THD's security-context from
                  inspection by SHOW PROCESSLIST while we're updating it. Nested
                  acquiring of LOCK_thd_data is fine (see below).
                */
                MUTEX_LOCK(grd_secctx, &thd->LOCK_thd_security_ctx);

                int auth_rc;
                thd->status_var.com_other++;

                thd->cleanup_connection();
                USER_CONN       *save_user_connect = const_cast<USER_CONN *>(thd->get_user_connect());
                LEX_CSTRING      save_db           = thd->db();
                Security_context save_security_ctx(*(thd->security_context()));

                auth_rc = acl_authenticate(thd, COM_CHANGE_USER);
                auth_rc |= mysql_audit_notify(thd, AUDIT_EVENT(MYSQL_AUDIT_CONNECTION_CHANGE_USER));
                if (auth_rc) {
                        *thd->security_context() = save_security_ctx;
                        thd->set_user_connect(save_user_connect);
                        thd->reset_db(save_db);

                        my_error(ER_ACCESS_DENIED_CHANGE_USER_ERROR,
                                 MYF(0),
                                 thd->security_context()->user().str,
                                 thd->security_context()->host_or_ip().str,
                                 (thd->password ? ER_THD(thd, ER_YES) : ER_THD(thd, ER_NO)));
                        thd->killed = THD::KILL_CONNECTION;
                        error       = true;
                } else {
#ifdef HAVE_PSI_THREAD_INTERFACE
                        /* we've authenticated new user */
                        PSI_THREAD_CALL(notify_session_change_user)
                        (thd->get_psi());
#endif /* HAVE_PSI_THREAD_INTERFACE */

                        if (save_user_connect)
                                decrease_user_connections(save_user_connect);
                        mysql_mutex_lock(&thd->LOCK_thd_data);
                        my_free(const_cast<char *>(save_db.str));
                        save_db = NULL_CSTR;
                        mysql_mutex_unlock(&thd->LOCK_thd_data);
                }
                break;
        }
        case COM_STMT_EXECUTE: {
                /* Clear possible warnings from the previous command */
                thd->reset_for_next_command();

                Prepared_statement *stmt = nullptr;
                if (!mysql_stmt_precheck(thd, com_data, command, &stmt)) {
                        PS_PARAM *parameters = com_data->com_stmt_execute.parameters;
                        copy_bind_parameter_values(thd, parameters, com_data->com_stmt_execute.parameter_count);

                        mysqld_stmt_execute(thd,
                                            stmt,
                                            com_data->com_stmt_execute.has_new_types,
                                            com_data->com_stmt_execute.open_cursor,
                                            parameters);
                        thd->bind_parameter_values       = nullptr;
                        thd->bind_parameter_values_count = 0;
                }
                break;
        }
        case COM_STMT_FETCH: {
                /* Clear possible warnings from the previous command */
                thd->reset_for_next_command();

                Prepared_statement *stmt = nullptr;
                if (!mysql_stmt_precheck(thd, com_data, command, &stmt))
                        mysqld_stmt_fetch(thd, stmt, com_data->com_stmt_fetch.num_rows);

                break;
        }
        case COM_STMT_SEND_LONG_DATA: {
                Prepared_statement *stmt;
                thd->get_stmt_da()->disable_status();
                if (!mysql_stmt_precheck(thd, com_data, command, &stmt))
                        mysql_stmt_get_longdata(thd,
                                                stmt,
                                                com_data->com_stmt_send_long_data.param_number,
                                                com_data->com_stmt_send_long_data.longdata,
                                                com_data->com_stmt_send_long_data.length);
                break;
        }
        case COM_STMT_PREPARE: {
                /* Clear possible warnings from the previous command */
                thd->reset_for_next_command();
                Prepared_statement *stmt = nullptr;

                DBUG_EXECUTE_IF("parser_stmt_to_error_log",
                                { LogErr(INFORMATION_LEVEL, ER_PARSER_TRACE, com_data->com_stmt_prepare.query); });
                DBUG_EXECUTE_IF("parser_stmt_to_error_log_with_system_prio",
                                { LogErr(SYSTEM_LEVEL, ER_PARSER_TRACE, com_data->com_stmt_prepare.query); });

                if (!mysql_stmt_precheck(thd, com_data, command, &stmt))
                        mysqld_stmt_prepare(thd, com_data->com_stmt_prepare.query, com_data->com_stmt_prepare.length, stmt);
                break;
        }
        case COM_STMT_CLOSE: {
                Prepared_statement *stmt = nullptr;
                thd->get_stmt_da()->disable_status();
                if (!mysql_stmt_precheck(thd, com_data, command, &stmt))
                        mysqld_stmt_close(thd, stmt);
                break;
        }
        case COM_STMT_RESET: {
                /* Clear possible warnings from the previous command */
                thd->reset_for_next_command();

                Prepared_statement *stmt = nullptr;
                if (!mysql_stmt_precheck(thd, com_data, command, &stmt))
                        mysqld_stmt_reset(thd, stmt);
                break;
        }
        case COM_QUERY: {
                assert(thd->m_digest == nullptr);
                thd->m_digest = &thd->m_digest_state;
                thd->m_digest->reset(thd->m_token_array, max_digest_length);

                if (alloc_query(thd, com_data->com_query.query, com_data->com_query.length))
                        break; // fatal error is set

                const char *packet_end = thd->query().str + thd->query().length;

                if (opt_general_log_raw)
                        query_logger.general_log_write(thd, command, thd->query().str, thd->query().length);

                DBUG_PRINT("query", ("%-.4096s", thd->query().str));

#if defined(ENABLED_PROFILING)
                thd->profiling->set_query_source(thd->query().str, thd->query().length);
#endif

                const LEX_CSTRING orig_query = thd->query();

                Parser_state parser_state;
                if (parser_state.init(thd, thd->query().str, thd->query().length))
                        break;

                parser_state.m_input.m_has_digest = true;

                // we produce digest if it's not explicitly turned off
                // by setting maximum digest length to zero
                if (get_max_digest_length() != 0)
                        parser_state.m_input.m_compute_digest = true;

                // Initially, prepare and optimize the statement for the primary
                // storage engine. If an eligible secondary storage engine is
                // found, the statement may be reprepared for the secondary
                // storage engine later.
                const auto saved_secondary_engine = thd->secondary_engine_optimization();
                thd->set_secondary_engine_optimization(Secondary_engine_optimization::PRIMARY_TENTATIVELY);

                copy_bind_parameter_values(thd, com_data->com_query.parameters, com_data->com_query.parameter_count);

                dispatch_sql_command(thd, &parser_state);

                // Check if the statement failed and needs to be restarted in
                // another storage engine.
                check_secondary_engine_statement(thd, &parser_state, orig_query.str, orig_query.length);

                thd->set_secondary_engine_optimization(saved_secondary_engine);

                DBUG_EXECUTE_IF("parser_stmt_to_error_log", { LogErr(INFORMATION_LEVEL, ER_PARSER_TRACE, thd->query().str); });
                DBUG_EXECUTE_IF("parser_stmt_to_error_log_with_system_prio",
                                { LogErr(SYSTEM_LEVEL, ER_PARSER_TRACE, thd->query().str); });

                while (!thd->killed && (parser_state.m_lip.found_semicolon != nullptr) && !thd->is_error()) {
                        /*
                          Multiple queries exits, execute them individually
                        */
                        const char *beginning_of_next_stmt = parser_state.m_lip.found_semicolon;

                        /* Finalize server status flags after executing a
                         * statement. */
                        thd->update_slow_query_status();
                        thd->send_statement_status();

                        const std::string &cn = Command_names::str_global(command);
                        mysql_audit_notify(thd,
                                           AUDIT_EVENT(MYSQL_AUDIT_GENERAL_STATUS),
                                           thd->get_stmt_da()->is_error() ? thd->get_stmt_da()->mysql_errno() : 0,
                                           cn.c_str(),
                                           cn.length());

                        size_t length = static_cast<size_t>(packet_end - beginning_of_next_stmt);

                        log_slow_statement(thd);

                        thd->reset_copy_status_var();

                        /* Remove garbage at start of query */
                        while (length > 0 && my_isspace(thd->charset(), *beginning_of_next_stmt)) {
                                beginning_of_next_stmt++;
                                length--;
                        }

                        /* PSI end */
                        MYSQL_END_STATEMENT(thd->m_statement_psi, thd->get_stmt_da());
                        thd->m_statement_psi = nullptr;
                        thd->m_digest        = nullptr;

/* SHOW PROFILE end */
#if defined(ENABLED_PROFILING)
                        thd->profiling->finish_current_query();
#endif

/* SHOW PROFILE begin */
#if defined(ENABLED_PROFILING)
                        thd->profiling->start_new_query("continuing");
                        thd->profiling->set_query_source(beginning_of_next_stmt, length);
#endif

                        mysql_thread_set_secondary_engine(false);

                        /* PSI begin */
                        thd->m_digest = &thd->m_digest_state;
                        thd->m_digest->reset(thd->m_token_array, max_digest_length);

                        thd->m_statement_psi = MYSQL_START_STATEMENT(&thd->m_statement_state,
                                                                     com_statement_info[command].m_key,
                                                                     thd->db().str,
                                                                     thd->db().length,
                                                                     thd->charset(),
                                                                     nullptr);
                        THD_STAGE_INFO(thd, stage_starting);

                        thd->set_query(beginning_of_next_stmt, length);
                        thd->set_query_id(next_query_id());
                        /*
                          Count each statement from the client.
                        */
                        thd->status_var.questions++;
                        thd->set_time(); /* Reset the query start time. */
                        parser_state.reset(beginning_of_next_stmt, length);
                        thd->set_secondary_engine_optimization(Secondary_engine_optimization::PRIMARY_TENTATIVELY);
                        /* TODO: set thd->lex->sql_command to SQLCOM_END here */
                        dispatch_sql_command(thd, &parser_state);

                        check_secondary_engine_statement(thd, &parser_state, beginning_of_next_stmt, length);

                        thd->set_secondary_engine_optimization(saved_secondary_engine);
                }

                thd->bind_parameter_values       = nullptr;
                thd->bind_parameter_values_count = 0;

                /* Need to set error to true for graceful shutdown */
                if ((thd->lex->sql_command == SQLCOM_SHUTDOWN) && (thd->get_stmt_da()->is_ok()))
                        error = true;

                DBUG_PRINT("info", ("query ready"));
                break;
        }
        case COM_FIELD_LIST: // This isn't actually needed
        {
                char *fields;
                /* Locked closure of all tables */
                LEX_STRING table_name;
                LEX_STRING db;
                push_deprecated_warn(thd, "COM_FIELD_LIST", "SHOW COLUMNS FROM statement");
                /*
                  SHOW statements should not add the used tables to the list of
                  tables used in a transaction.
                */
                MDL_savepoint mdl_savepoint = thd->mdl_context.mdl_savepoint();

                thd->status_var.com_stat[SQLCOM_SHOW_FIELDS]++;
                if (thd->copy_db_to(&db.str, &db.length))
                        break;
                thd->convert_string(&table_name,
                                    system_charset_info,
                                    (char *)com_data->com_field_list.table_name,
                                    com_data->com_field_list.table_name_length,
                                    thd->charset());
                Ident_name_check ident_check_status = check_table_name(table_name.str, table_name.length);
                if (ident_check_status == Ident_name_check::WRONG) {
                        /* this is OK due to convert_string() null-terminating
                         * the string */
                        my_error(ER_WRONG_TABLE_NAME, MYF(0), table_name.str);
                        break;
                } else if (ident_check_status == Ident_name_check::TOO_LONG) {
                        my_error(ER_TOO_LONG_IDENT, MYF(0), table_name.str);
                        break;
                }
                mysql_reset_thd_for_next_command(thd);
                lex_start(thd);
                /* Must be before we init the table list. */
                if (lower_case_table_names && !is_infoschema_db(db.str, db.length))
                        table_name.length = my_casedn_str(files_charset_info, table_name.str);
                Table_ref table_list(db.str, db.length, table_name.str, table_name.length, table_name.str, TL_READ);
                /*
                  Init Table_ref members necessary when the undelrying
                  table is view.
                */
                table_list.query_block = thd->lex->query_block;
                thd->lex->query_block->m_table_list.link_in_list(&table_list, &table_list.next_local);
                thd->lex->add_to_query_tables(&table_list);

                if (is_infoschema_db(table_list.db, table_list.db_length)) {
                        ST_SCHEMA_TABLE *schema_table = find_schema_table(thd, table_list.alias);
                        if (schema_table)
                                table_list.schema_table = schema_table;
                }

                if (!(fields = (char *)thd->memdup(com_data->com_field_list.query, com_data->com_field_list.query_length)))
                        break;
                // Don't count end \0
                thd->set_query(fields, com_data->com_field_list.query_length - 1);
                query_logger.general_log_print(thd, command, "%s %s", table_list.table_name, fields);

                if (open_temporary_tables(thd, &table_list))
                        break;

                if (check_table_access(thd, SELECT_ACL, &table_list, true, UINT_MAX, false))
                        break;

                thd->lex->sql_command = SQLCOM_SHOW_FIELDS;
                // See comment in
                // opt_trace_disable_if_no_security_context_access()
                Opt_trace_start ots(thd, &table_list, thd->lex->sql_command, nullptr, nullptr, 0, nullptr, nullptr);

                mysqld_list_fields(thd, &table_list, fields);

                thd->lex->cleanup(true);
                /* No need to rollback statement transaction, it's not started.
                 */
                assert(thd->get_transaction()->is_empty(Transaction_ctx::STMT));
                close_thread_tables(thd);
                thd->mdl_context.rollback_to_savepoint(mdl_savepoint);

                if (thd->transaction_rollback_request) {
                        /*
                          Transaction rollback was requested since MDL deadlock
                          was discovered while trying to open tables. Rollback
                          transaction in all storage engines including binary
                          log and release all locks.
                        */
                        trans_rollback_implicit(thd);
                        thd->mdl_context.release_transactional_locks();
                }

                thd->cleanup_after_query();
                thd->lex->destroy();
                break;
        }
        case COM_QUIT:
                /* Prevent results of the form, "n>0 rows sent, 0 bytes sent" */
                thd->set_sent_row_count(0);
                /* We don't calculate statistics for this command */
                query_logger.general_log_print(thd, command, NullS);
                // Don't give 'abort' message
                // TODO: access of protocol_classic should be removed
                if (thd->is_classic_protocol())
                        thd->get_protocol_classic()->get_net()->error = NET_ERROR_UNSET;
                thd->get_stmt_da()->disable_status(); // Don't send anything back
                error = true;                         // End server
                break;
        case COM_BINLOG_DUMP_GTID:
                // TODO: access of protocol_classic should be removed
                error = com_binlog_dump_gtid(thd,
                                             (char *)thd->get_protocol_classic()->get_raw_packet(),
                                             thd->get_protocol_classic()->get_packet_length());
                break;
        case COM_BINLOG_DUMP:
                // TODO: access of protocol_classic should be removed
                error = com_binlog_dump(thd,
                                        (char *)thd->get_protocol_classic()->get_raw_packet(),
                                        thd->get_protocol_classic()->get_packet_length());
                break;
        case COM_REFRESH: {
                int not_used;
                push_deprecated_warn(thd, "COM_REFRESH", "FLUSH statement");
                /*
                  Initialize thd->lex since it's used in many base functions,
                  such as open_tables(). Otherwise, it remains uninitialized and
                  may cause crash during execution of COM_REFRESH.
                */
                lex_start(thd);

                thd->status_var.com_stat[SQLCOM_FLUSH]++;
                ulong options = (ulong)com_data->com_refresh.options;
                if (trans_commit_implicit(thd))
                        break;
                thd->mdl_context.release_transactional_locks();
                if (check_global_access(thd, RELOAD_ACL))
                        break;
                query_logger.general_log_print(thd, command, NullS);
#ifndef NDEBUG
                bool debug_simulate = false;
                DBUG_EXECUTE_IF("simulate_detached_thread_refresh", debug_simulate = true;);
                if (debug_simulate) {
                        /*
                          Simulate a reload without a attached thread session.
                          Provides a environment similar to that of when the
                          server receives a SIGHUP signal and reloads caches
                          and flushes tables.
                        */
                        bool res;
                        current_thd = nullptr;
                        res         = handle_reload_request(nullptr, options | REFRESH_FAST, nullptr, &not_used);
                        current_thd = thd;
                        if (res)
                                break;
                } else
#endif
                        if (handle_reload_request(thd, options, (Table_ref *)nullptr, &not_used))
                        break;
                if (trans_commit_implicit(thd))
                        break;
                close_thread_tables(thd);
                thd->mdl_context.release_transactional_locks();
                thd->lex->destroy();
                my_ok(thd);
                break;
        }
        case COM_STATISTICS: {
                System_status_var current_global_status_var;
                ulong             uptime;
                size_t            length [[maybe_unused]];
                ulonglong         queries_per_second1000;
                char              buff[250];
                size_t            buff_len = sizeof(buff);

                query_logger.general_log_print(thd, command, NullS);
                thd->status_var.com_stat[SQLCOM_SHOW_STATUS]++;
                mysql_mutex_lock(&LOCK_status);
                calc_sum_of_all_status(&current_global_status_var);
                mysql_mutex_unlock(&LOCK_status);
                if (!(uptime = (ulong)(thd->query_start_in_secs() - server_start_time)))
                        queries_per_second1000 = 0;
                else
                        queries_per_second1000 = thd->query_id * 1000LL / uptime;

                length = snprintf(buff,
                                  buff_len - 1,
                                  "Uptime: %lu  Threads: %d  Questions: %lu  "
                                  "Slow queries: %llu  Opens: %llu  Flush tables: %lu  "
                                  "Open tables: %u  Queries per second avg: %u.%03u",
                                  uptime,
                                  (int)thd_manager->get_thd_count(),
                                  (ulong)thd->query_id,
                                  current_global_status_var.long_query_count,
                                  current_global_status_var.opened_tables,
                                  refresh_version,
                                  table_cache_manager.cached_tables(),
                                  (uint)(queries_per_second1000 / 1000),
                                  (uint)(queries_per_second1000 % 1000));
                // TODO: access of protocol_classic should be removed.
                // should be rewritten using store functions
                if (thd->get_protocol_classic()->write(pointer_cast<const uchar *>(buff), length))
                        break;
                if (thd->get_protocol()->flush())
                        break;
                thd->get_stmt_da()->disable_status();
                break;
        }
        case COM_PING:
                thd->status_var.com_other++;
                my_ok(thd); // Tell client we are alive
                break;
        case COM_PROCESS_INFO:
                bool        global_access;
                LEX_CSTRING db_saved;
                thd->status_var.com_stat[SQLCOM_SHOW_PROCESSLIST]++;
                push_deprecated_warn(thd, "COM_PROCESS_INFO", "SHOW PROCESSLIST statement");
                global_access = (check_global_access(thd, PROCESS_ACL) == 0);
                if (!thd->security_context()->priv_user().str[0] && !global_access)
                        break;
                query_logger.general_log_print(thd, command, NullS);
                db_saved = thd->db();

                DBUG_EXECUTE_IF("force_db_name_to_null", thd->reset_db(NULL_CSTR););

                mysqld_list_processes(thd, global_access ? NullS : thd->security_context()->priv_user().str, false, false);

                DBUG_EXECUTE_IF("force_db_name_to_null", thd->reset_db(db_saved););
                break;
        case COM_PROCESS_KILL: {
                push_deprecated_warn(thd, "COM_PROCESS_KILL", "KILL CONNECTION/QUERY statement");
                if (thd_manager->get_thread_id() & (~0xfffffffful))
                        my_error(ER_DATA_OUT_OF_RANGE, MYF(0), "thread_id", "mysql_kill()");
                else {
                        thd->status_var.com_stat[SQLCOM_KILL]++;
                        sql_kill(thd, com_data->com_kill.id, false);
                }
                break;
        }
        case COM_SET_OPTION: {
                thd->status_var.com_stat[SQLCOM_SET_OPTION]++;

                switch (com_data->com_set_option.opt_command) {
                case (int)MYSQL_OPTION_MULTI_STATEMENTS_ON:
                        // TODO: access of protocol_classic should be removed
                        thd->get_protocol_classic()->add_client_capability(CLIENT_MULTI_STATEMENTS);
                        my_eof(thd);
                        break;
                case (int)MYSQL_OPTION_MULTI_STATEMENTS_OFF:
                        thd->get_protocol_classic()->remove_client_capability(CLIENT_MULTI_STATEMENTS);
                        my_eof(thd);
                        break;
                default:
                        my_error(ER_UNKNOWN_COM_ERROR, MYF(0));
                        break;
                }
                break;
        }
        case COM_DEBUG:
                thd->status_var.com_other++;
                if (check_global_access(thd, SUPER_ACL))
                        break; /* purecov: inspected */
                query_logger.general_log_print(thd, command, NullS);
                my_eof(thd);
#ifdef WITH_LOCK_ORDER
                LO_dump();
#endif /* WITH_LOCK_ORDER */
                break;
        case COM_SLEEP:
        case COM_CONNECT:        // Impossible here
        case COM_TIME:           // Impossible from client
        case COM_DELAYED_INSERT: // INSERT DELAYED has been removed.
        case COM_END:
        default:
                my_error(ER_UNKNOWN_COM_ERROR, MYF(0));
                break;
        }

done:
        assert(thd->open_tables == nullptr || (thd->locked_tables_mode == LTM_LOCK_TABLES));

        /* Finalize server status flags after executing a command. */
        thd->update_slow_query_status();
        if (thd->killed)
                thd->send_kill_message();
        thd->send_statement_status();

        /* After sending response, switch to clone protocol */
        if (clone_cmd != nullptr) {
                assert(command == COM_CLONE);
                error = clone_cmd->execute_server(thd);
        }

        if (command == COM_SUBSCRIBE_GROUP_REPLICATION_STREAM && !error) {
                call_gr_incoming_connection_cb(
                        thd,
                        thd->active_vio->mysql_socket.fd,
                        thd->active_vio->ssl_arg ? static_cast<SSL *>(thd->active_vio->ssl_arg) : nullptr);
        }

        thd->rpl_thd_ctx.session_gtids_ctx().notify_after_response_packet(thd);

        if (!thd->is_error() && !thd->killed)
                mysql_audit_notify(thd, AUDIT_EVENT(MYSQL_AUDIT_GENERAL_RESULT), 0, nullptr, 0);

        const std::string &cn = Command_names::str_global(command);
        mysql_audit_notify(thd,
                           AUDIT_EVENT(MYSQL_AUDIT_GENERAL_STATUS),
                           thd->get_stmt_da()->is_error() ? thd->get_stmt_da()->mysql_errno() : 0,
                           cn.c_str(),
                           cn.length());

        /* command_end is informational only. The plugin cannot abort
           execution of the command at this point. */
        mysql_audit_notify(thd, AUDIT_EVENT(MYSQL_AUDIT_COMMAND_END), command, cn.c_str());

        log_slow_statement(thd);

        THD_STAGE_INFO(thd, stage_cleaning_up);

        thd->reset_query();
        thd->set_command(COM_SLEEP);
        thd->set_proc_info(nullptr);
        thd->lex->sql_command = SQLCOM_END;

        /* Performance Schema Interface instrumentation, end */
        MYSQL_END_STATEMENT(thd->m_statement_psi, thd->get_stmt_da());
        thd->m_statement_psi = nullptr;
        thd->m_digest        = nullptr;
        thd->reset_query_for_display();

        /* Prevent rewritten query from getting "stuck" in SHOW PROCESSLIST. */
        thd->reset_rewritten_query();

        thd_manager->dec_thread_running();

        /* Freeing the memroot will leave the THD::work_part_info invalid. */
        thd->work_part_info = nullptr;

        /*
          If we've allocated a lot of memory (compared to the default
          preallocation size = 8192; note that we don't actually preallocate
          anymore), free it so that one big query won't cause us to hold on to a
          lot of RAM forever. If not, keep the last block so that the next query
          will hopefully be able to run without allocating memory from the OS.

          The factor 5 is pretty much arbitrary, but ends up allowing three
          allocations (1 + 1.5 + 1.5²) under the current allocation policy.
        */
        constexpr size_t kPreallocSz = 40960;
        if (thd->mem_root->allocated_size() < kPreallocSz)
                thd->mem_root->ClearForReuse();
        else
                thd->mem_root->Clear();

                /* SHOW PROFILE instrumentation, end */
#if defined(ENABLED_PROFILING)
        thd->profiling->finish_current_query();
#endif

        return error;
}

```













