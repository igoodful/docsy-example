---
title: 事务语句
description: 事务语句
date: 2023-10-30
weight: 50


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

MySQL 不支持嵌套事务。

InnoDB 开启内部事务，是为了改变表中数据，所以，内部事务都以读写事务的身份启动。

用户事务可能会读取、改变表中数据，根据执行的第一条 SQL 语句不同，以不同身份启动：

- 执行的第一条 SQL 语句是 select、update、delete，以读事务身份启动事务。
- 执行的第一条 SQL 语句是 insert，以读写事务身份启动事务。
- 如果只读事务执行的第一条 SQL 语句是插入记录到用户临时表的 insert，也会分配事务 ID。

{{</alert>}}


```sql
show engine innodb status;

```




```sql
-- 用于开始一个新的读写事务,这两类语句都不需立即创建一致性读视图，事务的启动将延迟至实际需要时。只会给执行这些语句的线程打上 OPTION_BEGIN 标记，在这之后执行第一条 SQL 时，才会真正的启动事务。
begin
begin work
start transaction
start transaction read write

-- 用于开始一个新的只读事务,这两类语句都不需立即创建一致性读视图，事务的启动将延迟至实际需要时。只会给执行这些语句的线程打上 OPTION_BEGIN 标记，在这之后执行第一条 SQL 时，才会真正的启动事务。
start transaction read only

-- 用于开始一个新的读写事务,这两类语句都会先启动事务，随后立即创建一致性读视图。
start transaction with consistent snapshot
start transaction with consistent snapshot, read write

-- 用于开始一个新的只读事务,这两类语句都会先启动事务，随后立即创建一致性读视图。
start transaction with consistent snapshot, read only

start transaction with consistent snapshot, read write, read only
start transaction read write, read only

```

如果要投票选出我们最常用于开始一个事务的语句，大概非 BEGIN 莫属了。

接下来，我们就用 BEGIN 作为语句 1 ~ 5 的代表，来聊聊开始一个新事务的过程中，MySQL 做的那些事。

如果用一个词语描述 BEGIN 语句要做的事，那就是辞旧迎新，展开来说，BEGIN 语句主要做两件事：

- 辞旧：提交老事务。
- 迎新：准备新事务。


我们先来看一个场景：

在 MySQL 客户端命令行（mysql）中，我们通过 BEGIN 语句开始了一个事务（事务 1），并且已经执行了一条 INSERT 语句。

事务 1 还没有提交（即处于活跃状态），我们在同一个连接中又执行了 BEGIN 语句，事务 1 会发生什么？

答案是：事务 1 会被提交。

原因是：MySQL 不支持嵌套事务。事务 1 没有提交的情况下，又要开始一个新事务，事务 1 将无处安放，只能被动提交了。




## trans_begin

```c++
bool trans_begin(THD *thd, uint flags) {
        bool res = false;
        DBUG_TRACE;

        if (trans_check_state(thd))
                return true;

        TX_TRACKER_GET(tst);

        thd->locked_tables_list.unlock_locked_tables(thd);

        assert(!thd->locked_tables_mode);

        if (thd->in_multi_stmt_transaction_mode() || (thd->variables.option_bits & OPTION_TABLE_LOCK)) {
                thd->variables.option_bits &= ~OPTION_TABLE_LOCK;
                thd->server_status &= ~(SERVER_STATUS_IN_TRANS | SERVER_STATUS_IN_TRANS_READONLY);
                DBUG_PRINT("info", ("clearing SERVER_STATUS_IN_TRANS"));
                res = ha_commit_trans(thd, true);
        }

        thd->variables.option_bits &= ~OPTION_BEGIN;
        thd->get_transaction()->reset_unsafe_rollback_flags(Transaction_ctx::SESSION);

        if (res)
                return true;

        /*
          Release transactional metadata locks only after the
          transaction has been committed.
        */
        thd->mdl_context.release_transactional_locks();

        // The RO/RW options are mutually exclusive.
        assert(!((flags & MYSQL_START_TRANS_OPT_READ_ONLY) && (flags & MYSQL_START_TRANS_OPT_READ_WRITE)));
        if (flags & MYSQL_START_TRANS_OPT_READ_ONLY) {
                thd->tx_read_only = true;
                if (tst)
                        tst->set_read_flags(thd, TX_READ_ONLY);
        } else if (flags & MYSQL_START_TRANS_OPT_READ_WRITE) {
                /*
                  Explicitly starting a RW transaction when the server is in
                  read-only mode, is not allowed unless the user has SUPER priv.
                  Implicitly starting a RW transaction is allowed for backward
                  compatibility.
                */
                if (check_readonly(thd, true))
                        return true;
                thd->tx_read_only = false;
                /*
                  This flags that tx_read_only was set explicitly, rather than
                  just from the session's default.
                */
                if (tst)
                        tst->set_read_flags(thd, TX_READ_WRITE);
        }

        DBUG_EXECUTE_IF("dbug_set_high_prio_trx", {
                assert(thd->tx_priority == 0);
                thd->tx_priority = 1;
        });

        thd->variables.option_bits |= OPTION_BEGIN;
        thd->server_status |= SERVER_STATUS_IN_TRANS;
        if (thd->tx_read_only)
                thd->server_status |= SERVER_STATUS_IN_TRANS_READONLY;
        DBUG_PRINT("info", ("setting SERVER_STATUS_IN_TRANS"));

        if (tst)
                tst->add_trx_state(thd, TX_EXPLICIT);

        /* ha_start_consistent_snapshot() relies on OPTION_BEGIN flag set. */
        if (flags & MYSQL_START_TRANS_OPT_WITH_CONS_SNAPSHOT) {
                if (tst)
                        tst->add_trx_state(thd, TX_WITH_SNAPSHOT);
                res = ha_start_consistent_snapshot(thd);
        }

        /*
          Register transaction start in performance schema if not done already.
          We handle explicitly started transactions here, implicitly started
          transactions (and single-statement transactions in autocommit=1 mode)
          are handled in trans_register_ha().
          We can't handle explicit transactions in the same way as implicit
          because we want to correctly attribute statements which follow
          BEGIN but do not touch any transactional tables.
        */
#ifdef HAVE_PSI_TRANSACTION_INTERFACE
        if (thd->m_transaction_psi == nullptr) {
                thd->m_transaction_psi = MYSQL_START_TRANSACTION(&thd->m_transaction_state,
                                                                 nullptr,
                                                                 nullptr,
                                                                 thd->transaction_isolation,
                                                                 thd->tx_read_only,
                                                                 false);
                DEBUG_SYNC(thd, "after_set_transaction_psi_before_set_transaction_gtid");
                gtid_set_performance_schema_values(thd);
        }
#endif

        return res;
}


```



```c++
        inline bool in_multi_stmt_transaction_mode() const {
                return variables.option_bits & (OPTION_NOT_AUTOCOMMIT | OPTION_BEGIN);
        }

```

回到本小节主题，我们来看看 BEGIN 语句提交老事务的流程。

首先，BEGIN 语句会判断当前连接中是否有可能存在未提交事务，判断逻辑为：当前连接的线程是否被打上了 OPTION_NOT_AUTOCOMMIT 或 OPTION_BEGIN 标志位（如下代码所示）。

只要 variables.option_bits 包含其中一个标志位，就说明当前连接中可能存在未提交事务。

BEGIN 语句想要开始一个新事务，就必须先执行一次提交操作，把可能未提交的事务给提交了（如下代码所示）。

如果 variables.option_bits 没有包含两个标志位中的任何一个，说明当前连接中没有未提交事务，可以直接开始一个新事务。


辞旧完事，就该迎新了。

由于 MySQL 一向秉持不铺张浪费的原则，对于资源，能少分配就少分配、能晚分配就晚分配。

启动事务也需要分配资源，遵循不铺张浪费的原则，BEGIN 语句执行过程中，并不会马上启动一个新事务，只会为新事务做一点点准备工作。

这个一点点真的是一点点，你看：

thd->variables.option_bits |= OPTION_BEGIN;
上面的准备工作就是给当前连接的线程打上 OPTION_BEGIN 标志。

有了 OPTION_BEGIN 标志，MySQL 就不会每次执行完一条 SQL 语句就提交事务，而是需要用户发起 commit 语句才提交事务，这样的事务就可以执行多条 SQL 了。


**读事务**

- 事务真正启动于执行第一条 SQL 语句时，如果第一条 SQL 语句是 select、update、delete，事务会以读事务的身份启动。

- 读事务启动时，没有分配事务 ID 和回滚段，事务对象也没有加入到 trx_sys->rw_trx_list 链表。

根据我们使用 MySQL 的经验，以读事务身份启动的事务，不仅能正常执行改变（插入、更新、删除）表中数据的操作，还支持 MVCC、回滚。

对于启动时没有分配事务 ID 和回滚段的读事务来说，这是怎么做到的呢？

有一句话能够很好的回答这个问题，就是以发展的眼光看问题！

以读事务身份启动的事务，并不意味着一直都是读事务，它可以在某些时间点变成读写事务。



#### 只读事务

只读事务不能改变（插入、更新、删除）系统表、用户普通表的数据，但是能改变用户临时表的数据。

改变用户临时表的数据，同样需要为事务分配事务 ID，为用户临时表分配回滚段。根据只读事务执行的第一条 SQL 语句不同，这两个操作发生的时间点也可以分为两类。










