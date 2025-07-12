---
title: sql_mode
description: sql_mode
date: 2025-02-12
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

- https://bugs.mysql.com/bug.php?id=106121

{{< /alert >}}


## sql_mode


```viz-dot
digraph "sql_mode" {
        rankdir = LR;

        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

"sql_mode" -> "PIPES_AS_CONCAT";
"sql_mode" -> "ONLY_FULL_GROUP_BY";
"sql_mode" -> "STRICT_TRANS_TABLES";
"sql_mode" -> "NO_ZERO_IN_DATE";
"sql_mode" -> "NO_ZERO_DATE";
"sql_mode" -> "ERROR_FOR_DIVISION_BY_ZERO";
"sql_mode" -> "NO_AUTO_CREATE_USER";
"sql_mode" -> "ONLY_FULL_GROUP_BY";
"sql_mode" -> "ONLY_FULL_GROUP_BY";

}
```


## STRICT_TRANS_TABLES

在该模式下,如果一个值不能插入到一个事务表中,则中断当前的操作,对非事务表不做限制

## PIPES_AS_CONCAT

将"||"视为字符串的连接操作符而非或运算符,这和Oracle、pg数据库是一样的,也和字符串的拼接函数Concat相类似

## NO_AUTO_CREATE_USER

禁止GRANT创建密码为空的用户

## ERROR_FOR_DIVISION_BY_ZERO

在INSERT或UPDATE过程中,如果数据被零除,则产生错误而非警告。如果未给出该模式,那么数据被零除时MySQL返回NULL


## NO_ZERO_DATE

设置该值,mysql数据库不允许插入零日期,插入零日期会抛出错误而不是警告。


## NO_ZERO_IN_DATE

在严格模式下,不允许日期和月份为零


## ONLY_FULL_GROUP_BY

对于GROUP BY聚合操作,如果在SELECT中的列,没有在GROUP BY中出现,那么这个SQL是不合法的,因为列不在GROUP BY从句中




