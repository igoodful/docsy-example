---
title: execute
description: execute
date: 2023-10-30
weight: 10001


---

{{< alert >}}


{{< /alert >}}



```c++
bool Sql_cmd_dml::execute(THD *thd) {
  DBUG_TRACE;

  lex = thd->lex;

  Query_expression *const unit = lex->unit;

  bool statement_timer_armed = false;
  bool error_handler_active = false;

  Ignore_error_handler ignore_handler;
  Strict_error_handler strict_handler;

  // If statement is preparable, it must be prepared
  assert(owner() == nullptr || is_prepared());
  // If statement is regular, it must be unprepared
  assert(!is_regular() || !is_prepared());
  // If statement is part of SP, it can be both prepared and unprepared.

  // If a timer is applicable to statement, then set it.
  if (is_timer_applicable_to_statement(thd))
    statement_timer_armed = set_statement_timer(thd);

  if (is_data_change_stmt()) {
    // Push ignore / strict error handler
    if (lex->is_ignore()) {
      thd->push_internal_handler(&ignore_handler);
      error_handler_active = true;
      /*
        UPDATE IGNORE can be unsafe. We therefore use row based
        logging if mixed or row based logging is available.
        TODO: Check if the order of the output of the select statement is
        deterministic. Waiting for BUG#42415
      */
      if (lex->sql_command == SQLCOM_UPDATE)
        lex->set_stmt_unsafe(LEX::BINLOG_STMT_UNSAFE_UPDATE_IGNORE);
    } else if (thd->is_strict_mode()) {
      thd->push_internal_handler(&strict_handler);
      error_handler_active = true;
    }
  }

  if (!is_prepared()) {
    if (prepare(thd)) goto err;
  } else {
    /*
      Prepared statement, open tables referenced in statement and check
      privileges for it.
    */
    cleanup(thd);
    if (open_tables_for_query(thd, lex->query_tables, 0)) goto err;
#ifndef NDEBUG
    if (sql_command_code() == SQLCOM_SELECT)
      DEBUG_SYNC(thd, "after_table_open");
#endif
    // Bind table and field information
    if (restore_cmd_properties(thd)) return true;
    if (check_privileges(thd)) goto err;

    if (m_lazy_result) {
      Prepared_stmt_arena_holder ps_arena_holder(thd);

      if (result->prepare(thd, *unit->get_unit_column_types(), unit)) goto err;
      m_lazy_result = false;
    }
  }

  if (validate_use_secondary_engine(lex)) goto err;

  lex->set_exec_started();

  DBUG_EXECUTE_IF("use_attachable_trx",
                  thd->begin_attachable_ro_transaction(););

  THD_STAGE_INFO(thd, stage_init);

  thd->clear_current_query_costs();

  // Replication may require extra check of data change statements
  if (is_data_change_stmt() && run_before_dml_hook(thd)) goto err;

  // Revertable changes are not supported during preparation
  assert(thd->change_list.is_empty());

  assert(!lex->is_query_tables_locked());
  /*
    Locking of tables is done after preparation but before optimization.
    This allows to do better partition pruning and avoid locking unused
    partitions. As a consequence, in such a case, prepare stage can rely only
    on metadata about tables used and not data from them.
  */
  if (!is_empty_query()) {
    if (lock_tables(thd, lex->query_tables, lex->table_count, 0)) goto err;
  }

  // Perform statement-specific execution
  if (execute_inner(thd)) goto err;

  // Count the number of statements offloaded to a secondary storage engine.
  if (using_secondary_storage_engine() && lex->unit->is_executed())
    ++thd->status_var.secondary_engine_execution_count;

  assert(!thd->is_error());

  // Pop ignore / strict error handler
  if (error_handler_active) thd->pop_internal_handler();

  THD_STAGE_INFO(thd, stage_end);

  // Do partial cleanup (preserve plans for EXPLAIN).
  lex->cleanup(thd, false);
  lex->clear_values_map();
  lex->set_secondary_engine_execution_context(nullptr);

  // Perform statement-specific cleanup for Query_result
  if (result != nullptr) result->cleanup(thd);

  thd->save_current_query_costs();

  thd->update_previous_found_rows();

  DBUG_EXECUTE_IF("use_attachable_trx", thd->end_attachable_transaction(););

  if (statement_timer_armed && thd->timer) reset_statement_timer(thd);

  /*
    This sync point is normally right before thd->query_plan is reset, so
    EXPLAIN FOR CONNECTION can catch the plan. It is copied here as
    after unprepare() EXPLAIN considers the query as "not ready".
    @todo remove in WL#6570 when unprepare() is gone.
  */
  DEBUG_SYNC(thd, "before_reset_query_plan");

  return false;

err:
  assert(thd->is_error() || thd->killed);
  DBUG_PRINT("info", ("report_error: %d", thd->is_error()));
  THD_STAGE_INFO(thd, stage_end);

  lex->cleanup(thd, false);
  lex->clear_values_map();
  lex->set_secondary_engine_execution_context(nullptr);

  // Abort and cleanup the result set (if it has been prepared).
  if (result != nullptr) {
    result->abort_result_set(thd);
    result->cleanup(thd);
  }
  if (error_handler_active) thd->pop_internal_handler();

  if (statement_timer_armed && thd->timer) reset_statement_timer(thd);

  /*
    There are situations where we want to know the cost of a query that
    has failed during execution, e.g because of a timeout.
  */
  thd->save_current_query_costs();

  DBUG_EXECUTE_IF("use_attachable_trx", thd->end_attachable_transaction(););

  return thd->is_error();
}

```









