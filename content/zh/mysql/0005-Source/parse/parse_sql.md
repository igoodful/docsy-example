---
title: parse_sql
description: parse_sql
date: 2023-10-30
weight: 10001


---

{{< alert >}}

这段代码是 MySQL 8.0.26 中用于分派和执行 SQL 命令的核心函数。它执行了诸如解析 SQL、执行命令、写入通用日志等关键操作，并且在出现错误时进行相应的处理和记录。




{{< /alert >}}


### sql/sql_parse.cc
```c++
bool parse_sql(THD *thd, Parser_state *parser_state,
               Object_creation_ctx *creation_ctx) {
  DBUG_TRACE;
  bool ret_value;
  assert(thd->m_parser_state == nullptr);
  // TODO fix to allow parsing gcol exprs after main query.
  //  assert(thd->lex->m_sql_cmd == NULL);

  /* Backup creation context. */

  Object_creation_ctx *backup_ctx = nullptr;

  if (creation_ctx) backup_ctx = creation_ctx->set_n_backup(thd);

  /* Set parser state. */

  thd->m_parser_state = parser_state;

  parser_state->m_digest_psi = nullptr;
  parser_state->m_lip.m_digest = nullptr;

  if (thd->m_digest != nullptr) {
    /* Start Digest */
    parser_state->m_digest_psi = MYSQL_DIGEST_START(thd->m_statement_psi);

    if (parser_state->m_input.m_compute_digest ||
        (parser_state->m_digest_psi != nullptr)) {
      /*
        If either:
        - the caller wants to compute a digest
        - the performance schema wants to compute a digest
        set the digest listener in the lexer.
      */
      parser_state->m_lip.m_digest = thd->m_digest;
      parser_state->m_lip.m_digest->m_digest_storage.m_charset_number =
          thd->charset()->number;
    }
  }

  /* Parse the query. */

  /*
    Use a temporary DA while parsing. We don't know until after parsing
    whether the current command is a diagnostic statement, in which case
    we'll need to have the previous DA around to answer questions about it.
  */
  Diagnostics_area *parser_da = thd->get_parser_da();
  Diagnostics_area *da = thd->get_stmt_da();

  Parser_oom_handler poomh;
  // Note that we may be called recursively here, on INFORMATION_SCHEMA queries.

  thd->mem_root->set_max_capacity(thd->variables.parser_max_mem_size);
  thd->mem_root->set_error_for_capacity_exceeded(true);
  thd->push_internal_handler(&poomh);

  thd->push_diagnostics_area(parser_da, false);

  bool mysql_parse_status = thd->sql_parser();

  thd->pop_internal_handler();
  thd->mem_root->set_max_capacity(0);
  thd->mem_root->set_error_for_capacity_exceeded(false);
  /*
    Unwind diagnostics area.

    If any issues occurred during parsing, they will become
    the sole conditions for the current statement.

    Otherwise, if we have a diagnostic statement on our hands,
    we'll preserve the previous diagnostics area here so we
    can answer questions about it.  This specifically means
    that repeatedly asking about a DA won't clear it.

    Otherwise, it's a regular command with no issues during
    parsing, so we'll just clear the DA in preparation for
    the processing of this command.
  */

  if (parser_da->current_statement_cond_count() != 0) {
    /*
      Error/warning during parsing: top DA should contain parse error(s)!  Any
      pre-existing conditions will be replaced. The exception is diagnostics
      statements, in which case we wish to keep the errors so they can be sent
      to the client.
    */
    if (thd->lex->sql_command != SQLCOM_SHOW_WARNS &&
        thd->lex->sql_command != SQLCOM_GET_DIAGNOSTICS)
      da->reset_condition_info(thd);

    /*
      We need to put any errors in the DA as well as the condition list.
    */
    if (parser_da->is_error() && !da->is_error()) {
      da->set_error_status(parser_da->mysql_errno(), parser_da->message_text(),
                           parser_da->returned_sqlstate());
    }

    da->copy_sql_conditions_from_da(thd, parser_da);

    parser_da->reset_diagnostics_area();
    parser_da->reset_condition_info(thd);

    /*
      Do not clear the condition list when starting execution as it
      now contains not the results of the previous executions, but
      a non-zero number of errors/warnings thrown during parsing!
    */
    thd->lex->keep_diagnostics = DA_KEEP_PARSE_ERROR;
  }

  thd->pop_diagnostics_area();

  /*
    Check that if THD::sql_parser() failed either thd->is_error() is set, or an
    internal error handler is set.

    The assert will not catch a situation where parsing fails without an
    error reported if an error handler exists. The problem is that the
    error handler might have intercepted the error, so thd->is_error() is
    not set. However, there is no way to be 100% sure here (the error
    handler might be for other errors than parsing one).
  */

  assert(!mysql_parse_status || (mysql_parse_status && thd->is_error()) ||
         (mysql_parse_status && thd->get_internal_handler()));

  /* Reset parser state. */

  thd->m_parser_state = nullptr;

  /* Restore creation context. */

  if (creation_ctx) creation_ctx->restore_env(thd, backup_ctx);

  /* That's it. */

  ret_value = mysql_parse_status || thd->is_fatal_error();

  if ((ret_value == 0) && (parser_state->m_digest_psi != nullptr)) {
    /*
      On parsing success, record the digest in the performance schema.
    */
    assert(thd->m_digest != nullptr);
    MYSQL_DIGEST_END(parser_state->m_digest_psi,
                     &thd->m_digest->m_digest_storage);
  }

  return ret_value;
}

```

















