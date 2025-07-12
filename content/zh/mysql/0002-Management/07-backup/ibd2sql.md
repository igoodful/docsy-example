---
title: ibd2sql
description: ibd2sql
date: 2023-10-26
weight: 20000
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 简介

1. ibd2sql是一个使用纯python3编写的离线解析MYSQL INNODB存储引擎的ibd文件的工具

2. 无第三方依赖包

3. 使用GPL-3.0 license

4. 支持MySQL版本有： 5.X or 8.X 9.X

5. 作用：通过ibd文件提取DDL和原表数据

{{<alert>}}


- [ibd2sql](https://github.com/ddcw/ibd2sql.git)

{{</alert>}}


- 方便: 提取表DDL
- 实用: 可替换库(--schema)/表(--table)名, 可在sql语句中输出完整的字段(--complete)
- 简单: 纯python3代码编写, 无依赖包.
- 支持众多数据类型: 支持所有mysql数据类型
- 支持复杂的表结构: 分区表, 注释, 主键, 外键, 约束, 自增, 普通索引, 前缀索引, 主键前缀索引, 唯一索引, 复合索引, 默认值, 符号, 虚拟字段, INSTANT, 无主键等情况的表
- 数据误删恢复: 可以输出被标记为deleted的数据
- 安全: 离线解析ibd文件, 仅可读权限即可


## 案例分享


### 举例一：通过ibd文件直接提取表结构和数据


- 表结构和数据

```sql
[root@127.0.0.1:8032 09:43:44(db1)]$ show create table employee\G
*************************** 1. row ***************************
       Table: employee
Create Table: CREATE TABLE `employee` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
  `dname` varchar(20) COLLATE utf8mb4_bin DEFAULT NULL,
  `eid` varchar(20) COLLATE utf8mb4_bin DEFAULT NULL,
  `ename` varchar(20) COLLATE utf8mb4_bin DEFAULT NULL,
  `hiredate` date DEFAULT NULL,
  `salary` double DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=25 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
1 row in set (0.01 sec)

[root@127.0.0.1:8032 09:44:01(db1)]$ select * from employee;
+----+-----------+------+--------+------------+--------+
| id | dname     | eid  | ename  | hiredate   | salary |
+----+-----------+------+--------+------------+--------+
|  1 | 研发部    | 1001 | 刘备   | 2021-11-01 |   3000 |
|  3 | 研发部    | 1002 | 关羽   | 2021-11-02 |   5000 |
|  5 | 研发部    | 1003 | 张飞   | 2021-11-03 |   7000 |
|  7 | 研发部    | 1004 | 赵云   | 2021-11-04 |   7000 |
|  9 | 研发部    | 1005 | 马超   | 2021-11-05 |   4000 |
| 11 | 研发部    | 1006 | 黄忠   | 2021-11-06 |   4000 |
| 13 | 销售部    | 1007 | 曹操   | 2021-11-01 |   2000 |
| 15 | 销售部    | 1008 | 许褚   | 2021-11-02 |   3000 |
| 17 | 销售部    | 1009 | 典韦   | 2021-11-03 |   5000 |
| 19 | 销售部    | 1010 | 张辽   | 2021-11-04 |   6000 |
| 21 | 销售部    | 1011 | 徐晃   | 2021-11-05 |   9000 |
| 23 | 销售部    | 1012 | 曹洪   | 2021-11-06 |   6000 |
+----+-----------+------+--------+------------+--------+
12 rows in set (0.00 sec)


```

- ibd2sql解析

```sql
2024-10-12T09:41:47 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $ python3 main.py /db/storage/guolicheng/mysql_8032/data/db1/employee.ibd --sql --ddl
CREATE TABLE IF NOT EXISTS `db1`.`employee`(
    `id` bigint unsigned NOT NULL AUTO_INCREMENT,
    `dname` varchar(20) NULL,
    `eid` varchar(20) NULL,
    `ename` varchar(20) NULL,
    `hiredate` date NULL,
    `salary` double NULL,
    PRIMARY KEY  (`id` )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin ;
INSERT INTO `db1`.`employee` VALUES (1, '研发部', '1001', '刘备', '2021-11-1', 3000.0);
INSERT INTO `db1`.`employee` VALUES (3, '研发部', '1002', '关羽', '2021-11-2', 5000.0);
INSERT INTO `db1`.`employee` VALUES (5, '研发部', '1003', '张飞', '2021-11-3', 7000.0);
INSERT INTO `db1`.`employee` VALUES (7, '研发部', '1004', '赵云', '2021-11-4', 7000.0);
INSERT INTO `db1`.`employee` VALUES (9, '研发部', '1005', '马超', '2021-11-5', 4000.0);
INSERT INTO `db1`.`employee` VALUES (11, '研发部', '1006', '黄忠', '2021-11-6', 4000.0);
INSERT INTO `db1`.`employee` VALUES (13, '销售部', '1007', '曹操', '2021-11-1', 2000.0);
INSERT INTO `db1`.`employee` VALUES (15, '销售部', '1008', '许褚', '2021-11-2', 3000.0);
INSERT INTO `db1`.`employee` VALUES (17, '销售部', '1009', '典韦', '2021-11-3', 5000.0);
INSERT INTO `db1`.`employee` VALUES (19, '销售部', '1010', '张辽', '2021-11-4', 6000.0);
INSERT INTO `db1`.`employee` VALUES (21, '销售部', '1011', '徐晃', '2021-11-5', 9000.0);
INSERT INTO `db1`.`employee` VALUES (23, '销售部', '1012', '曹洪', '2021-11-6', 6000.0);

```



### 举例二：误删数据恢复场景

- 模拟误删数据
```sql
[root@127.0.0.1:8032 09:57:47(db1)]$ select * from test;
+----+-----------+------+--------+------------+--------+
| id | dname     | eid  | ename  | hiredate   | salary |
+----+-----------+------+--------+------------+--------+
|  1 | 研发部    | 1001 | 刘备   | 2021-11-01 |   3000 |
|  3 | 研发部    | 1002 | 关羽   | 2021-11-02 |   5000 |
|  5 | 研发部    | 1003 | 张飞   | 2021-11-03 |   7000 |
|  7 | 研发部    | 1004 | 赵云   | 2021-11-04 |   7000 |
|  9 | 研发部    | 1005 | 马超   | 2021-11-05 |   4000 |
| 11 | 研发部    | 1006 | 黄忠   | 2021-11-06 |   4000 |
| 13 | 销售部    | 1007 | 曹操   | 2021-11-01 |   2000 |
| 15 | 销售部    | 1008 | 许褚   | 2021-11-02 |   3000 |
| 17 | 销售部    | 1009 | 典韦   | 2021-11-03 |   5000 |
| 19 | 销售部    | 1010 | 张辽   | 2021-11-04 |   6000 |
| 21 | 销售部    | 1011 | 徐晃   | 2021-11-05 |   9000 |
| 23 | 销售部    | 1012 | 曹洪   | 2021-11-06 |   6000 |
+----+-----------+------+--------+------------+--------+
12 rows in set (0.00 sec)

[root@127.0.0.1:8032 09:57:59(db1)]$ delete from test where id>10 and id<19;
Query OK, 4 rows affected (0.03 sec)

[root@127.0.0.1:8032 09:58:36(db1)]$ select * from test;
+----+-----------+------+--------+------------+--------+
| id | dname     | eid  | ename  | hiredate   | salary |
+----+-----------+------+--------+------------+--------+
|  1 | 研发部    | 1001 | 刘备   | 2021-11-01 |   3000 |
|  3 | 研发部    | 1002 | 关羽   | 2021-11-02 |   5000 |
|  5 | 研发部    | 1003 | 张飞   | 2021-11-03 |   7000 |
|  7 | 研发部    | 1004 | 赵云   | 2021-11-04 |   7000 |
|  9 | 研发部    | 1005 | 马超   | 2021-11-05 |   4000 |
| 19 | 销售部    | 1010 | 张辽   | 2021-11-04 |   6000 |
| 21 | 销售部    | 1011 | 徐晃   | 2021-11-05 |   9000 |
| 23 | 销售部    | 1012 | 曹洪   | 2021-11-06 |   6000 |
+----+-----------+------+--------+------------+--------+
8 rows in set (0.00 sec)

[root@127.0.0.1:8032 09:58:41(db1)]$ show create table test\G
*************************** 1. row ***************************
       Table: test
Create Table: CREATE TABLE `test` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
  `dname` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL,
  `eid` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL,
  `ename` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL,
  `hiredate` date DEFAULT NULL,
  `salary` double DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=25 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
1 row in set (0.00 sec)

```


- 解析出表结构、解析出数据、解析表和数据、解析被误删的数据
```bash
2024-10-12T09:59:37 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $ python3 main.py /db/storage/guolicheng/mysql_8032/data/db1/test.ibd --ddl
CREATE TABLE IF NOT EXISTS `db1`.`test`(
    `id` bigint unsigned NOT NULL AUTO_INCREMENT,
    `dname` varchar(20) NULL,
    `eid` varchar(20) NULL,
    `ename` varchar(20) NULL,
    `hiredate` date NULL,
    `salary` double NULL,
    PRIMARY KEY  (`id` )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin ;
2024-10-12T10:00:36 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $ python3 main.py /db/storage/guolicheng/mysql_8032/data/db1/test.ibd --sql
INSERT INTO `db1`.`test` VALUES (1, '研发部', '1001', '刘备', '2021-11-1', 3000.0);
INSERT INTO `db1`.`test` VALUES (3, '研发部', '1002', '关羽', '2021-11-2', 5000.0);
INSERT INTO `db1`.`test` VALUES (5, '研发部', '1003', '张飞', '2021-11-3', 7000.0);
INSERT INTO `db1`.`test` VALUES (7, '研发部', '1004', '赵云', '2021-11-4', 7000.0);
INSERT INTO `db1`.`test` VALUES (9, '研发部', '1005', '马超', '2021-11-5', 4000.0);
INSERT INTO `db1`.`test` VALUES (19, '销售部', '1010', '张辽', '2021-11-4', 6000.0);
INSERT INTO `db1`.`test` VALUES (21, '销售部', '1011', '徐晃', '2021-11-5', 9000.0);
INSERT INTO `db1`.`test` VALUES (23, '销售部', '1012', '曹洪', '2021-11-6', 6000.0);
2024-10-12T10:00:41 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $ python3 main.py /db/storage/guolicheng/mysql_8032/data/db1/test.ibd --ddl --sql
CREATE TABLE IF NOT EXISTS `db1`.`test`(
    `id` bigint unsigned NOT NULL AUTO_INCREMENT,
    `dname` varchar(20) NULL,
    `eid` varchar(20) NULL,
    `ename` varchar(20) NULL,
    `hiredate` date NULL,
    `salary` double NULL,
    PRIMARY KEY  (`id` )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin ;
INSERT INTO `db1`.`test` VALUES (1, '研发部', '1001', '刘备', '2021-11-1', 3000.0);
INSERT INTO `db1`.`test` VALUES (3, '研发部', '1002', '关羽', '2021-11-2', 5000.0);
INSERT INTO `db1`.`test` VALUES (5, '研发部', '1003', '张飞', '2021-11-3', 7000.0);
INSERT INTO `db1`.`test` VALUES (7, '研发部', '1004', '赵云', '2021-11-4', 7000.0);
INSERT INTO `db1`.`test` VALUES (9, '研发部', '1005', '马超', '2021-11-5', 4000.0);
INSERT INTO `db1`.`test` VALUES (19, '销售部', '1010', '张辽', '2021-11-4', 6000.0);
INSERT INTO `db1`.`test` VALUES (21, '销售部', '1011', '徐晃', '2021-11-5', 9000.0);
INSERT INTO `db1`.`test` VALUES (23, '销售部', '1012', '曹洪', '2021-11-6', 6000.0);
2024-10-12T10:00:52 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $ python3 main.py /db/storage/guolicheng/mysql_8032/data/db1/test.ibd --delete --sql
INSERT INTO `db1`.`test` VALUES (17, '销售部', '1009', '典韦', '2021-11-3', 5000.0);
INSERT INTO `db1`.`test` VALUES (15, '销售部', '1008', '许褚', '2021-11-2', 3000.0);
INSERT INTO `db1`.`test` VALUES (13, '销售部', '1007', '曹操', '2021-11-1', 2000.0);
INSERT INTO `db1`.`test` VALUES (11, '研发部', '1006', '黄忠', '2021-11-6', 4000.0);
2024-10-12T10:01:02 [igoodfu@48 /db/storage/guolicheng/tmp/ibd2sql-1.6] $

```





### 举例三：ibd文件数据页损坏场景

如果ibd文件数据页损坏, 则可以跳过该页, 或者暴力解析.

对于想并发解析的, 也可以使用本方法.

如果是ibd文件的元数据信息损坏, 则要先恢复元数据信息. 然后使用--sdi-table选项指定正确的元数据信息文件.

我这里没有做重定向, 是直接打印在屏幕上的(方便演示)

```bash
filename="/tmp/ddcw_alltype_table.ibd" # 要解析的ibd文件名
python3 main.py ${filename} --ddl # 获取表结构信息
filesize=`stat -c %s ${filename}`
maxpagecount=$[ ${filesize} / 16384 ]
current_page=1
while [ ${current_page} -le ${maxpagecount} ];do
	echo "-- ${filename} PAGE NO: ${current_page}";
	current_page=$[ ${current_page} + 1 ]
	python3 main.py ${filename} --sql --page-start ${current_page} --page-count 1 2>/dev/null ;
done

```











