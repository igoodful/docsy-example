---
title: git log
description: git log
date: 2023-10-12
weight: 60000


---

{{< alert >}}

{{< /alert >}}



### 查看某个tag的创建时间
```bash
[root@dev mysql-server]#  git show --no-patch --pretty=format:'%C(yellow)%h%Creset | %Cgreen%an%Creset | %Cblue%ad%Creset | %Cred%s%Creset' mysql-8.0.26
beb865a960b | mysql-builder@oracle.com | Thu Jul 1 07:15:50 2021 +0200 |
[root@dev mysql-server]#  git log --tags --simplify-by-decoration --pretty="format:%ai %d"|grep mysql-8.0.26
2021-07-01 07:15:50 +0200  (tag: mysql-cluster-8.0.26, tag: mysql-8.0.26)
[root@dev mysql-server]#
```



###

```bash
commit f6bb5e7cc5e57f44c881a3f63ee507102c3e398d
Author: Pedro Figueiredo <pedro.figueiredo@oracle.com>
Date:   Mon Sep 20 10:50:26 2021 +0100

    BUG#32891221 REPLICA HANGS WITH REPLICA_PRESERVE_COMMIT_ORDER ON

    Description
    -----------
    When a replica with `replica_preserve_commit_order = 1` is submitted to an
    intensive workload for a long time, the applier workers will, eventually,
    wait indefinetly on the commit order queue.

    Analysis
    --------
    In the logic around the commit order sequence ticket, there was a variable
    defined in `Commit_order_manager::finish_one` with `auto` that should be
    defined as `unsigned long long`. The side effect of this was that when the
    ticket number reached max value for `long long` + 1, it was casted to `int`
    resulting in a value of `1`, which has a special value for the implemented
    logic (the sequence generator is frozen and no numbers can be generated)
    and, therefore, making the applier hang.

    Also, with little effort and changes, one could make the overall heuristcs
    work when the sequence ticket reaches the `unsigned long long` max value
    (2^64), by letting wrap around and skipping `0` and `1` which have special
    values for the implemented logic.

    Fix
    ---
    1. Change the type of the variable in `Commit_order_manager::finish_one`
       from `auto` to `cs::apply::Commit_order_queue::sequence_type`.
    2. When generating a new ticket sequence number, keep generating while the
       value is less or equal than 1.

    Reviewed-by: Kapil Agrawal <kapil.agrawal@oracle.com>
    Reviewed-by: Neha Kumari <neha.n.kumari@oracle.com>
    RB: 26572

```




















