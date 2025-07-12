---
title: 1. explain
description: explain
date: 2023-10-31
weight: 1000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |


- [https://github.com/Percona-Lab/mysql_random_data_load](https://github.com/Percona-Lab/mysql_random_data_load)





## 一、准备

#### 1. 建表
```sql
CREATE TABLE doc (
pk_id bigint(18) unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
id int(18) NOT NULL COMMENT '业务id',
title varchar(100) NOT NULL COMMENT '标题',
keywords varchar(100) NOT NULL COMMENT '关键字',
preview_img varchar(255) NOT NULL COMMENT '预览KanoIds',
cover varchar(100) NOT NULL COMMENT '封面图，kanoId',
file_id varchar(20) NOT NULL COMMENT '文档页编号',
version varchar(20) NOT NULL COMMENT '最新版本号',
user_name varchar(20) NOT NULL COMMENT '权属人用户名称',
user_id varchar(20) NOT NULL COMMENT '负责人/权属人OA用户id',
price int(11) DEFAULT NULL COMMENT '价格',
is_deleted tinyint(4) DEFAULT NULL COMMENT '是否删除',
gmt_created datetime NOT NULL COMMENT '创建时间',
gmt_modified datetime NOT NULL COMMENT '修改时间',
gmt_deleted datetime(3) NOT NULL DEFAULT '9999-12-31 23:59:59.000' COMMENT '逻辑删除时间',
PRIMARY KEY (pk_id),
UNIQUE KEY uniq_id (id),
KEY idx_user_file (user_name,file_id),
KEY idx_gmtCreated (gmt_created),
KEY idx_gmtModified (gmt_modified)
) DEFAULT CHARSET=utf8mb4 COMMENT='文档表';
```

#### 2. 造数
- [mysql_random_data_load](https://github.com/Percona-Lab/mysql_random_data_load)

```bash
mysql_random_data_load test doc  10000000 --user=admin --password='admin' --host=172.17.134.76 --port=3306 --debug
```








## 二、explain

| table         | json          | 含义 |      |
| :------------ | :------------ | :--- | :--- |
| id            | select_id     |      |      |
| select_type   | 无            |      |      |
| table         | table_name    |      |      |
| partitions    | partitions    |      |      |
| type          | access_type   |      |      |
| possible_keys | possible_keys |      |      |
| key           | key           |      |      |
| key_len       | key_length    |      |      |
| ref           | ref           |      |      |
| rows          | rows          |      |      |
| filtered      | filtered      |      |      |
| Extra         | 无            |      |      |


以下情况会触发统计信息更新:
1. analyze table
2. 距离上一次更新统计信息，发生变化的行数超过一定数值时自动更新(transient: 1/16，persistent1/10)transient和persistent是两种采样方式
3. create table/truncate table 会初始化统计信息。


```sql
mysql> desc select * from apple.info;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | info  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  401 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)


[admin@127.0.0.1:3306 10:06:06(performance_schema)]$desc select * from mysql.user;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

```

## 三、字段详解

#### 01. id

- id越大，则越先读
- id相同，则从上往下顺序依次读



#### 02. select_type



| select_type          | 说明                   | 示例 |
| :------------------- | :--------------------- | :--- |
| SIMPLE               | 没有union和子查询      |      |
| PRIMARY              | 最外层SELECT           |      |
| UNION                |                        |      |
| DEPENDENT UNION      |                        |      |
| UNION RESULT         |                        |      |
| SUBQUERY             | SELECT子查询中的第一个 |      |
| DEPENDENT SUBQUERY   |                        |      |
| DERIVED              | 派生表                 |      |
| DEPENDENT DERIVED    | 派生表依赖于另一个表   |      |
| MATERIALIZED         | 物化子查询             |      |
| UNCACHEABLE SUBQUERY |                        |      |
| UNCACHEABLE UNION    |                        |      |












#### 03. table
表示当前这一行正在访问哪张表，如果SQL定义了别名，则展示表的别名

#### 04. partitions
当前查询匹配记录的分区。对于未分区的表，返回null


#### 05. type

10个值：system > const > eq_ref > ref > ref_or_null > index_merge > range > index > ALL

| type        | 说明                                                                                              | 示例 |
| :---------- | :------------------------------------------------------------------------------------------------ | :--- |
| All         | 正在等待客户端向它发送执行语句                                                                    |      |
| index       | 只遍历索引树，通常比All快                                                                         |      |
| range       | 索引范围查询，常见于使用 =, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, IN()或者like等运算符的查询中 |      |
| index_merge | 正在执行 kill 语句，杀死指定线程                                                                  |      |
| ref_or_null | 正在执行 kill 语句，杀死指定线程                                                                  |      |
| ref         | 该线程正在退出                                                                                    |      |
| eq_ref      | 正在关闭服务器                                                                                    |      |
| const       | 直接按主键或唯一键读取                                                                            |      |
| system      | 表只有一行记录（等于系统表），这是const类型的特列                                                 |      |
| NULL        | MySQL能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引                                   |      |


###### 1. ALL
> 全表扫描，性能最差










###### 2. index
全索引扫描

和ALL类似，只不过index是全盘扫描了索引的数据。当查询仅使用索引中的一部分列时，可使用此类型。有两种场景会触发：
如果索引是查询的覆盖索引，并且索引查询的数据就可以满足查询中所需的所有数据，则只扫描索引树。此时，explain的Extra 列的结果是Using index。index通常比ALL快，因为索引的大小通常小于表数据。
按索引的顺序来查找数据行，执行了全表扫描。此时，explain的Extra列的结果不会出现Uses index。

###### 3. range




###### 4. index_merge
表示索引合并，一般对多个二级索引列做or操作时就会发生索引合并

```sql
[admin@127.0.0.1:3306 14:42:45(test)]$ show create table const_table\G
*************************** 1. row ***************************
       Table: const_table
Create Table: CREATE TABLE `const_table` (
  `id` int NOT NULL,
  `name` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 14:43:11(test)]$ select * from const_table;
+----+------+
| id | name |
+----+------+
|  1 | 1    |
| 10 | 10   |
|  2 | 2    |
|  3 | 3    |
|  4 | 4    |
|  5 | 5    |
|  6 | 6    |
|  7 | 7    |
|  8 | 8    |
|  9 | 9    |
+----+------+
10 rows in set (0.00 sec)

[admin@127.0.0.1:3306 14:43:15(test)]$ desc select * from const_table where id=1 or name='2';
+----+-------------+-------------+------------+-------------+-------------------+-------------------+---------+------+------+----------+---------------------------------------------+
| id | select_type | table       | partitions | type        | possible_keys     | key               | key_len | ref  | rows | filtered | Extra                                       |
+----+-------------+-------------+------------+-------------+-------------------+-------------------+---------+------+------+----------+---------------------------------------------+
|  1 | SIMPLE      | const_table | NULL       | index_merge | PRIMARY,uniq_name | PRIMARY,uniq_name | 4,203   | NULL |    2 |   100.00 | Using union(PRIMARY,uniq_name); Using where |
+----+-------------+-------------+------------+-------------+-------------------+-------------------+---------+------+------+----------+---------------------------------------------+
1 row in set, 1 warning (0.00 sec)


```

###### 5. ref_or_null

###### 6. ref

{{< alert color="secondary" >}}

**等值查询**：

- 普通单列索引
- 普通联合索引的最左匹配原则

注意： 不能是主键和唯一索引

{{< /alert >}}


- 普通单列索引

普通单列索引:`KEY idx_gmtCreated (gmt_created)`

```sql
[admin@127.0.0.1:3306 15:11:41(test)]$ select count(*) from doc where gmt_created='2024-03-16 03:03:01' ;
+----------+
| count(*) |
+----------+
|        3 |
+----------+
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 15:11:56(test)]$ desc select * from doc where gmt_created='2024-03-16 03:03:01' ;
+----+-------------+-------+------------+------+----------------+----------------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys  | key            | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+----------------+----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | doc   | NULL       | ref  | idx_gmtCreated | idx_gmtCreated | 5       | const |    3 |   100.00 | NULL  |
+----+-------------+-------+------------+------+----------------+----------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

```


- 普通联合索引的最左匹配

普通联合索引：`KEY idx_user_file (user_name,file_id)`
```sql
[admin@127.0.0.1:3306 15:07:29(test)]$ select count(*) from doc where user_name='Cynthia Ferguson';
+----------+
| count(*) |
+----------+
|        9 |
+----------+
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 15:07:46(test)]$ desc select * from doc where user_name='Cynthia Ferguson';
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | doc   | NULL       | ref  | idx_user_file | idx_user_file | 82      | const |    9 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+---------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

```


###### 7. eq_ref
一般意味着在表关联时，被关联表上的关联列走的是主键或者唯一索引

- 主键索引(primary key)或者非空唯一索引(unique not null)等值关联

- 若主键或非空唯一索引是多个字段，则每个字段都必须参与等值关联



```sql
[admin@127.0.0.1:3306 14:27:31(test)]$ show create table user\G
*************************** 1. row ***************************
       Table: user
Create Table: CREATE TABLE `user` (
  `id` int NOT NULL,
  `name` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 14:28:40(test)]$ show create table user_balance\G
*************************** 1. row ***************************
       Table: user_balance
Create Table: CREATE TABLE `user_balance` (
  `uid` int NOT NULL,
  `balance` int DEFAULT NULL,
  PRIMARY KEY (`uid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 14:28:51(test)]$ select * from user;
+----+----------+
| id | name     |
+----+----------+
|  1 | ar414    |
|  2 | zhangsan |
|  3 | lisi     |
|  4 | wangwu   |
+----+----------+
4 rows in set (0.00 sec)

[admin@127.0.0.1:3306 14:29:01(test)]$ select * from user_balance;
+-----+---------+
| uid | balance |
+-----+---------+
|   1 |     100 |
|   2 |     200 |
|   3 |     300 |
|   4 |     400 |
|   5 |     500 |
+-----+---------+
5 rows in set (0.00 sec)

[admin@127.0.0.1:3306 14:29:11(test)]$ select * from user left join user_balance on user.id=user_balance.uid;
+----+----------+------+---------+
| id | name     | uid  | balance |
+----+----------+------+---------+
|  1 | ar414    |    1 |     100 |
|  2 | zhangsan |    2 |     200 |
|  3 | lisi     |    3 |     300 |
|  4 | wangwu   |    4 |     400 |
+----+----------+------+---------+
4 rows in set (0.00 sec)

[admin@127.0.0.1:3306 14:29:18(test)]$ desc select * from user left join user_balance on user.id=user_balance.uid;
+----+-------------+--------------+------------+--------+---------------+---------+---------+--------------+------+----------+-------+
| id | select_type | table        | partitions | type   | possible_keys | key     | key_len | ref          | rows | filtered | Extra |
+----+-------------+--------------+------------+--------+---------------+---------+---------+--------------+------+----------+-------+
|  1 | SIMPLE      | user         | NULL       | ALL    | NULL          | NULL    | NULL    | NULL         |    4 |   100.00 | NULL  |
|  1 | SIMPLE      | user_balance | NULL       | eq_ref | PRIMARY       | PRIMARY | 4       | test.user.id |    1 |   100.00 | NULL  |
+----+-------------+--------------+------------+--------+---------------+---------+---------+--------------+------+----------+-------+
2 rows in set, 1 warning (0.00 sec)

```






###### 8. const

> 主键或唯一索引的所有部分等值查询

用于将 "主键" 或 "唯一" 索引的所有部分与常量值进行比较

最多只返回一行数据. const 查询速度非常快, 因为它仅仅读取一次即可

示例如下：
```sql
[admin@127.0.0.1:3306 14:08:13(test)]$ show create table const_table\G
*************************** 1. row ***************************
       Table: const_table
Create Table: CREATE TABLE `const_table` (
  `id` int NOT NULL,
  `name` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[admin@127.0.0.1:3306 14:08:24(test)]$ select * from const_table;
+----+------+
| id | name |
+----+------+
|  1 | 1    |
|  2 | 2    |
|  3 | 3    |
|  4 | 4    |
|  5 | 5    |
+----+------+
5 rows in set (0.00 sec)

[admin@127.0.0.1:3306 14:08:30(test)]$ explain select * from const_table where id=3;
+----+-------------+-------------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
| id | select_type | table       | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | const_table | NULL       | const | PRIMARY       | PRIMARY | 4       | const |    1 |   100.00 | NULL  |
+----+-------------+-------------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

[admin@127.0.0.1:3306 14:08:37(test)]$ explain select * from const_table where name='4';
+----+-------------+-------------+------------+-------+---------------+-----------+---------+-------+------+----------+-------------+
| id | select_type | table       | partitions | type  | possible_keys | key       | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-------------+------------+-------+---------------+-----------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | const_table | NULL       | const | uniq_name     | uniq_name | 203     | const |    1 |   100.00 | Using index |
+----+-------------+-------------+------------+-------+---------------+-----------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)


```


###### 9. system












#### 06. possible_keys


#### 07. key
表示MySQL实际选择的索引

如果为NULL，则没有使用索引



#### 08. key_len
索引使用的字节数

#### 09. ref
表示将哪个字段或常量和key列所使用的字段进行比较

显示索引的哪一列被使用了，如果可能的话，是一个常数，哪些列或常量被用于查找索引列上的值

#### 10. rows
MySQL估算会扫描的行数，数值越小越好

#### 11. filtered
表示符合查询条件的数据百分比，最大100。用rows × filtered可获得和下一张表连接的行数。例如rows = 1000，filtered = 50%，则和下一张表连接的行数是500。

#### 12. Extra

| Extra             | 说明                             | 示例 |
| :---------------- | :------------------------------- | :--- |
| Using filesort    | 正在等待客户端向它发送执行语句   |      |
| Using tempporary  | 常见于order by和group by         |      |
| Using where       | 正在执行 kill 语句，杀死指定线程 |      |
| Using index       | 该线程正在退出                   |      |
| Using join buffer | 正在关闭服务器                   |      |
| distinct          | 一个从节点连上了主节点           |      |
| impossible where  | 一个从节点正在连主节点           |      |



- Using filesort 外部排序
表示当SQL中有一个地方需要对一些数据进行排序的时候，优化器找不到能够使用的索引，所以只能使用外部的索引排序，外部排序就不断的在磁盘和内存中交换数据，这样就摆脱不了很多次磁盘IO，以至于SQL执行的效率很低。


```sql
[admin@127.0.0.1:3306 12:02:40(performance_schema)]$desc select * from mysql.user order by authentication_string;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra          |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using filesort |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------+
1 row in set, 1 warning (0.00 sec)

```


###### Using tempporary
表示在对MySQL查询结果进行排序时，使用了临时表,,这样的查询效率是比外部排序更低的，常见于order by和group by。
- order by
- group by
- distinct
- union



```sql
[admin@127.0.0.1:3306 12:10:45((none))]$desc select authentication_string  from mysql.user group  by authentication_string;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-----------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra           |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-----------------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using temporary |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-----------------+
1 row in set, 1 warning (0.00 sec)

```

-  Using filesort; Using tempporary

两者都用上了，可谓是雪上加霜

```sql

[admin@127.0.0.1:3306 12:10:58((none))]$desc select authentication_string  from mysql.user group  by authentication_string order by authentication_string;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                           |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------+
|  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using temporary; Using filesort |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+---------------------------------+
1 row in set, 1 warning (0.00 sec)

```

###### Using index
表示实现了覆盖索引扫描；也就是需要访问的数据都在索引中，不需要回表



- Using where

使用了where但是好像没啥用



{{< alert >}}
索引里的列数最好不要超过5个

单表索引数，最好不要超过5个

低选择性的列不加索引，如性别列（联合索引另外考虑）

在组合索引中，常用的字段放在前面；选择性高的字段放在前面

需要经常排序的字段，可加到索引中，列顺序和最常用的排序一致

对较长的字符数据类型的字段建索引，优先考虑前缀索引

合理合并索引，避免冗余，例如(a,b)和(a)，应该去掉(a)。

对于分区表，结合业务选择合适的分区键，查询条件尽可能使用分区键

为避免隐式转换，数据类型与传入的数据类型要求一致

使用"OR"操作符时，若有个条件没有索引则全表扫描

使用"!="操作符时，极有可能无法使用索引

在使用联合索引时，记住前导原则，查询时如果缺少前导列，索引可能失效

合理使用USE INDEX和IGNORE INDEX进行索引的选择。
{{< /alert >}}




{{< alert color="secondary" title="explain" >}}
key_len 越小越好

rows 越小越好

能用到索引时，filtered越大越好，说明有更多的数据可利用索引过滤

不能用到索引时，filtered越小越好，说明需要过滤的数据更少

{{< /alert >}}

```sql
explain select * from t3 where f1 = 2 order by f2;
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | t3    | NULL       | ref  | f1            | f1   | 4       | const |   10 |   100.00 | Using index |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+

```

{{< alert color="secondary" title="type" >}}
type列说明
{{< /alert >}}
| 类型 | 描述 |
| :--- | :--- ||
|const          |	使用主键或唯一二级索引与常数进行等值匹配时|
|eq_ref         |	连接查询时，如果被驱动表是通过主键或者不允许为 null 的唯一索引列进行等值匹配的方法访问的|
|ref            |	通过普通的二级索引与常数进行等值匹配时|
|ref_or_null    |	对二级索引进行等值匹配且该索引的值也可以为 null 的时候|
|range          |	使用索引进行范围查询|
|index          |	使用索引覆盖，扫描前部索引记录的时候|
|ALL            |	全表扫描 需要优化|

{{< alert color="secondary" title="extra" >}}
extra列说明
{{< /alert >}}

| 类型 | 描述 |
| :--- | :--- ||
|Using index            |	使用索引覆盖的情况|
|Using index condition  |	可能索引条件下推|
|Using where            |	服务层可以把属于索引的一部分但又无法使用索引的条件下推到存储引擎层，而其他条件还是得在服务层过滤存储引擎层返回的数据。当出现这的情况，执行计划的 extra 字段就会出现"Using where"，它可以和"Using index" 、"Using index condition" 一起出现。只出现"Using where"时，type是all则需要优化。|
|using filesort         |	数据以排序的顺序被读取，可能未使用到索引|
|Using temporary        |	用临时表保存中间结果，常用于GROUP BY 和 ORDER BY操作中，一般看到它说明查询需要优化了|










综上所述，调优 MySQL 慢查询的一般流程如下：

- 识别查询（手动或使用 PMM 等工具）
- 检查查询的 EXPLAIN 计划
- 查看表定义
- 创建索引

在 MySQL 中，如果执行时间超过配置的`long_query_time`阈值，则查询被视为慢。该阈值可能会根据应用程序的要求和数据库环境而变化。 MySQL 的慢查询日志是一项功能，可以通过记录任何超过此执行时间限制的查询来帮助识别这些查询，从而可以进行进一步的分析和优化。




## 成本
>mysql-8.0.32

```sql
[root@127.0.0.1:8032 17:10:26(apple)]$ select * from mysql.server_cost;
+------------------------------+------------+---------------------+---------+---------------+
| cost_name                    | cost_value | last_update         | comment | default_value |
+------------------------------+------------+---------------------+---------+---------------+
| disk_temptable_create_cost   |       NULL | 2024-05-20 09:37:28 | NULL    |            20 |
| disk_temptable_row_cost      |       NULL | 2024-05-20 09:37:28 | NULL    |           0.5 |
| key_compare_cost             |       NULL | 2024-05-20 09:37:28 | NULL    |          0.05 |
| memory_temptable_create_cost |       NULL | 2024-05-20 09:37:28 | NULL    |             1 |
| memory_temptable_row_cost    |       NULL | 2024-05-20 09:37:28 | NULL    |           0.1 |
| row_evaluate_cost            |       NULL | 2024-05-20 09:37:28 | NULL    |           0.1 |
+------------------------------+------------+---------------------+---------+---------------+
6 rows in set (0.01 sec)

[root@127.0.0.1:8032 17:11:18(apple)]$ select * from mysql.engine_cost;
+-------------+-------------+------------------------+------------+---------------------+---------+---------------+
| engine_name | device_type | cost_name              | cost_value | last_update         | comment | default_value |
+-------------+-------------+------------------------+------------+---------------------+---------+---------------+
| default     |           0 | io_block_read_cost     |       NULL | 2024-05-20 09:37:28 | NULL    |             1 |
| default     |           0 | memory_block_read_cost |       NULL | 2024-05-20 09:37:28 | NULL    |          0.25 |
+-------------+-------------+------------------------+------------+---------------------+---------+---------------+
2 rows in set (0.00 sec)

[root@127.0.0.1:8032 17:11:33(apple)]$
```



```sql
CREATE TABLE `h_cc_subs_param_pk` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `SEQ_ID` decimal(12,0) DEFAULT NULL COMMENT '序列值',
  `MONTH` int NOT NULL COMMENT '月份',
  `BAK_TIME` datetime DEFAULT NULL COMMENT '备份时间',
  `SUBS_ID` decimal(12,0) NOT NULL COMMENT '产品订购标识',
  `SUBSCRIPTION_ID` decimal(12,0) DEFAULT NULL COMMENT '产品类型',
  `PARAM_ID` varchar(21) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin NOT NULL COMMENT '属性标识',
  `PARAM_VALUE` varchar(1000) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '属性值',
  `INURE_TIME` datetime DEFAULT NULL COMMENT '生效时间',
  `EXPIRE_TIME` datetime DEFAULT NULL COMMENT '失效时间',
  `TYPE` decimal(2,0) NOT NULL COMMENT '类型 0 产品 1 附属产品 2 体验产品',
  `CREATE_ID` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '创建工号',
  `CREATE_TIME` datetime DEFAULT NULL COMMENT '创建时间',
  `MODIFY_ID` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '修改工号',
  `MODIFY_CONTENT` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '修改说明',
  `REC_UPDATE_TIME` datetime DEFAULT NULL COMMENT '记录更新时间',
  `HISTORY_SEQ` decimal(12,0) DEFAULT NULL COMMENT '历史流水',
  `TRANSACTION_ID` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '订单流水',
  `BOSS_PARAM_TYPE` varchar(21) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '计费参数类型',
  `PARAM_GROUP` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_bin DEFAULT NULL COMMENT '产品属性组',
  PRIMARY KEY (`id`,`MONTH`),
  KEY `IDX_HCC_SUBS_PA_ID` (`PARAM_ID`),
  KEY `IDX_HCC_SUBS_PA_SUBS_ID` (`SUBS_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_bin
/*!50500 PARTITION BY RANGE  COLUMNS(`MONTH`)
(PARTITION P01 VALUES LESS THAN (2) ENGINE = InnoDB,
 PARTITION P02 VALUES LESS THAN (3) ENGINE = InnoDB,
 PARTITION P03 VALUES LESS THAN (4) ENGINE = InnoDB,
 PARTITION P04 VALUES LESS THAN (5) ENGINE = InnoDB,
 PARTITION P05 VALUES LESS THAN (6) ENGINE = InnoDB,
 PARTITION P06 VALUES LESS THAN (7) ENGINE = InnoDB,
 PARTITION P07 VALUES LESS THAN (8) ENGINE = InnoDB,
 PARTITION P08 VALUES LESS THAN (9) ENGINE = InnoDB,
 PARTITION P09 VALUES LESS THAN (10) ENGINE = InnoDB,
 PARTITION P10 VALUES LESS THAN (11) ENGINE = InnoDB,
 PARTITION P11 VALUES LESS THAN (12) ENGINE = InnoDB,
 PARTITION P12 VALUES LESS THAN (13) ENGINE = InnoDB,
 PARTITION P13 VALUES LESS THAN (14) ENGINE = InnoDB) */;


 GreatDB Cluster[customer]> show index from H_CC_SUBS_PARAM_PK;
+--------------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table              | Non_unique | Key_name                | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+--------------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| h_cc_subs_param_pk |          0 | PRIMARY                 |            1 | id          | A         |    72480976 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| h_cc_subs_param_pk |          0 | PRIMARY                 |            2 | MONTH       | A         |    73733488 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| h_cc_subs_param_pk |          1 | IDX_HCC_SUBS_PA_ID      |            1 | PARAM_ID    | A         |       64977 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| h_cc_subs_param_pk |          1 | IDX_HCC_SUBS_PA_SUBS_ID |            1 | SUBS_ID     | A         |    20298002 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+--------------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
4 rows in set (0.00 sec)


GreatDB Cluster[customer]> explain analyze select `ID`,`SEQ_ID`,`MONTH`,date_format(`BAK_TIME`,'%Y-%m-%d %H:%i:%s') as `BAK_TIME`,`SUBS_ID`,`SUBSCRIPTION_ID`,`PARAM_ID`,`PARAM_VALUE`,date_format(`INURE_TIME`,'%Y-%m-%d %H:%i:%s') as `INURE_TIME`,date_format(`EXPIRE_TIME`,'%Y-%m-%d %H:%i:%s') as `EXPIRE_TIME`,`TYPE`,`CREATE_ID`,date_format(`CREATE_TIME`,'%Y-%m-%d %H:%i:%s') as `CREATE_TIME`,`MODIFY_ID`,`MODIFY_CONTENT`,date_format(`REC_UPDATE_TIME`,'%Y-%m-%d %H:%i:%s') as `REC_UPDATE_TIME`,`HISTORY_SEQ`,`TRANSACTION_ID`,`BOSS_PARAM_TYPE`,`PARAM_GROUP` from  `apple`.`H_CC_SUBS_PARAM_PK` where  `id` > '29637' and `id` <= '29748' and MONTH=1;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((h_cc_subs_param_pk.`MONTH` = 1) and (h_cc_subs_param_pk.id > 29637) and (h_cc_subs_param_pk.id <= 29748))  (cost=0.35 rows=1) (actual time=52.983..14428.587 rows=111 loops=1)
    -> Table scan on H_CC_SUBS_PARAM_PK  (cost=0.35 rows=1) (actual time=0.028..13142.871 rows=7854188 loops=1)
 |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (14.44 sec)

GreatDB Cluster[customer]> explain analyze select `ID`,`SEQ_ID`,`MONTH`,date_format(`BAK_TIME`,'%Y-%m-%d %H:%i:%s') as `BAK_TIME`,`SUBS_ID`,`SUBSCRIPTION_ID`,`PARAM_ID`,`PARAM_VALUE`,date_format(`INURE_TIME`,'%Y-%m-%d %H:%i:%s') as `INURE_TIME`,date_format(`EXPIRE_TIME`,'%Y-%m-%d %H:%i:%s') as `EXPIRE_TIME`,`TYPE`,`CREATE_ID`,date_format(`CREATE_TIME`,'%Y-%m-%d %H:%i:%s') as `CREATE_TIME`,`MODIFY_ID`,`MODIFY_CONTENT`,date_format(`REC_UPDATE_TIME`,'%Y-%m-%d %H:%i:%s') as `REC_UPDATE_TIME`,`HISTORY_SEQ`,`TRANSACTION_ID`,`BOSS_PARAM_TYPE`,`PARAM_GROUP` from  `apple`.`H_CC_SUBS_PARAM_PK` where  `id` > '29637' and `id` <= '29748';
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                         |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((h_cc_subs_param_pk.id > 29637) and (h_cc_subs_param_pk.id <= 29748))  (cost=22.61 rows=111) (actual time=0.090..0.386 rows=111 loops=1)
    -> Index range scan on H_CC_SUBS_PARAM_PK using PRIMARY over (29637 < id <= 29748)  (cost=22.61 rows=111) (actual time=0.078..0.357 rows=111 loops=1)
 |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)


GreatDB Cluster[customer]> explain analyze select `ID`,`SEQ_ID`,`MONTH`,date_format(`BAK_TIME`,'%Y-%m-%d %H:%i:%s') as `BAK_TIME`,`SUBS_ID`,`SUBSCRIPTION_ID`,`PARAM_ID`,`PARAM_VALUE`,date_format(`INURE_TIME`,'%Y-%m-%d %H:%i:%s') as `INURE_TIME`,date_format(`EXPIRE_TIME`,'%Y-%m-%d %H:%i:%s') as `EXPIRE_TIME`,`TYPE`,`CREATE_ID`,date_format(`CREATE_TIME`,'%Y-%m-%d %H:%i:%s') as `CREATE_TIME`,`MODIFY_ID`,`MODIFY_CONTENT`,date_format(`REC_UPDATE_TIME`,'%Y-%m-%d %H:%i:%s') as `REC_UPDATE_TIME`,`HISTORY_SEQ`,`TRANSACTION_ID`,`BOSS_PARAM_TYPE`,`PARAM_GROUP` from  `apple`.`H_CC_SUBS_PARAM_PK` PARTITION(P01) force index(primary) where  `id` > '29637' and `id` <= '29748';
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                         |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((h_cc_subs_param_pk.id > 29637) and (h_cc_subs_param_pk.id <= 29748))  (cost=22.54 rows=111) (actual time=0.037..0.268 rows=111 loops=1)
    -> Index range scan on H_CC_SUBS_PARAM_PK using PRIMARY over (29637 < id <= 29748)  (cost=22.54 rows=111) (actual time=0.035..0.250 rows=111 loops=1)
 |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

GreatDB Cluster[customer]> explain analyze select `ID`,`SEQ_ID`,`MONTH`,date_format(`BAK_TIME`,'%Y-%m-%d %H:%i:%s') as `BAK_TIME`,`SUBS_ID`,`SUBSCRIPTION_ID`,`PARAM_ID`,`PARAM_VALUE`,date_format(`INURE_TIME`,'%Y-%m-%d %H:%i:%s') as `INURE_TIME`,date_format(`EXPIRE_TIME`,'%Y-%m-%d %H:%i:%s') as `EXPIRE_TIME`,`TYPE`,`CREATE_ID`,date_format(`CREATE_TIME`,'%Y-%m-%d %H:%i:%s') as `CREATE_TIME`,`MODIFY_ID`,`MODIFY_CONTENT`,date_format(`REC_UPDATE_TIME`,'%Y-%m-%d %H:%i:%s') as `REC_UPDATE_TIME`,`HISTORY_SEQ`,`TRANSACTION_ID`,`BOSS_PARAM_TYPE`,`PARAM_GROUP` from  `apple`.`H_CC_SUBS_PARAM_PK` where  `id` > '29637' and `id` <= '29748' and month=1;
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((h_cc_subs_param_pk.`MONTH` = 1) and (h_cc_subs_param_pk.id > 29637) and (h_cc_subs_param_pk.id <= 29748))  (cost=0.35 rows=1) (actual time=58.754..15533.322 rows=111 loops=1)
    -> Table scan on H_CC_SUBS_PARAM_PK  (cost=0.35 rows=1) (actual time=0.034..14243.216 rows=7854188 loops=1)
 |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (15.53 sec)

```





## explain format=json




## explain format=tree



- `Index range scan on` 索引扫描
- `Filter:` 过滤条件
- `Materialize with deduplication` 物化成临时表
- `Nested loop inner join` 内连接，执行在前面的为作为驱动表，表之间采用嵌套循环连接算法执行



## explain analize

> MySQL 8.0.18 开始支持


## explain for connection processlist_id;

> 在线获取SQL的执行计划
























