---
title: 双主
description: double master
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;



## 参数详解

让服务器在正常工作负载下运行一段时间并监控日志。如果此步骤导致日志中出现任何警告，请调整应用程序，使其仅使用与 GTID 兼容的功能并且不会生成任何警告。

{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在进行下一步之前，您必须确保错误日志中没有生成任何警告

{{</alert>}}

#### auto_increment_offset

确定列值的起点


#### auto_increment_increment

控制连续列值之间的间隔




## 使用
第二列开始的计算如下：

列值计算公式：$auto\\_increment\\_offset + N \times auto\\_increment\\_increment$，其中$N$是序列 $[1, 2, 3, \cdot\cdot\cdot]$ 中的正整数值。

#### 单数

```sql
[root@127.0.0.1:8032 18:11:37(apple)]$ set session auto_increment_offset=1;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 18:11:48(apple)]$ set session auto_increment_increment=2;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 18:11:53(apple)]$ show variables like 'auto_inc%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| auto_increment_increment | 2     |
| auto_increment_offset    | 1     |
+--------------------------+-------+
2 rows in set (0.01 sec)

[root@127.0.0.1:8032 18:12:00(apple)]$ create table autoinc1 (col int not null auto_increment primary key);
Query OK, 0 rows affected (0.04 sec)

[root@127.0.0.1:8032 18:12:13(apple)]$ insert into autoinc1 values (null), (null), (null), (null);
Query OK, 4 rows affected (0.04 sec)
Records: 4  Duplicates: 0  Warnings: 0

[root@127.0.0.1:8032 18:12:27(apple)]$ select * from  autoinc1;
+-----+
| col |
+-----+
|   1 |
|   3 |
|   5 |
|   7 |
+-----+
4 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:12:36(apple)]$


```


#### 双数

```sql
[root@127.0.0.1:8032 18:05:49(apple)]$ set session auto_increment_increment=2;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 18:06:05(apple)]$ set session auto_increment_offset=2;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 18:06:22(apple)]$ create table autoinc2 (col int not null auto_increment primary key);
Query OK, 0 rows affected (0.06 sec)

[root@127.0.0.1:8032 18:06:54(apple)]$ insert into autoinc2 values (null), (null), (null), (null);
Query OK, 4 rows affected (0.04 sec)
Records: 4  Duplicates: 0  Warnings: 0

[root@127.0.0.1:8032 18:07:06(apple)]$ select * from  autoinc2;
+-----+
| col |
+-----+
|   2 |
|   4 |
|   6 |
|   8 |
+-----+
4 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:07:13(apple)]$

```





