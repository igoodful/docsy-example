---
title: charset
description: max_connections
date: 2023-10-25
weight: 20000


viz: true
---

{{< alert >}}


{{< /alert >}}






## 一、服务器的字符集


#### character_set_server
变量character_set_server定义服务器的字符集。其值将用作任何CREATE DATABASE命令的默认字符集

```sql
[dbscale_internal@172.17.135.83:16310 16:35:51((none))]$ show global variables like 'character_set_server';
+----------------------+---------+
| Variable_name        | Value   |
+----------------------+---------+
| character_set_server | utf8mb4 |
+----------------------+---------+
1 row in set (0.04 sec)

```

#### character_set_database
此变量`character_set_database`仅影响不包含显式字符集的LOAD DATA命令

```sql
[dbscale_internal@172.17.135.83:16310 16:52:05((none))]$ show global variables like 'character_set_database';
+------------------------+---------+
| Variable_name          | Value   |
+------------------------+---------+
| character_set_database | utf8mb4 |
+------------------------+---------+
1 row in set (0.00 sec)

```

- 您不应该配置此变量，因为它的值会自动从默认数据库继承
- 如果未设置默认数据库，则会话将使用全局值character_set_server。该变量已被弃用，修改它会生成警告



## 二、数据库的字符集
> 数据库的字符集将影响所有没有显式字符集创建的表和存储过程

更改数据库字符集：
- 如果所有新表没有显式字符集值，则它们将使用新的默认值
- 现有表的字符集不会改变



```sql
[dbscale_internal@172.17.135.83:16310 16:53:55((none))]$ show create database apple\G
*************************** 1. row ***************************
       Database: apple
Create Database: CREATE DATABASE `apple` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.01 sec)


```

修改数据库的字符集
```sql
alter database database_name default character set latin1;
```




- 若创建库命令上没有显式设置表字符集或排序规则，则库的字符集继承`character_set_server`的字符集
- 若创建库命令上显式设置表字符集或排序规则，则库的字符为指定的字符集或排序规则








## 三、表的字符集

```sql
[dbscale_internal@172.17.135.83:16310 16:55:07(apple)]$ show create table sbtest1\G
*************************** 1. row ***************************
       Table: sbtest1
Create Table: CREATE TABLE `sbtest1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB AUTO_INCREMENT=5000001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```

- 若创建表命令上没有显式设置表字符集或排序规则，则表的字符集继承自库的字符集
- 若创建表命令上有显式设置表字符集或排序规则，则表的字符集为指定的字符集或排序规则







## 四、列的字符集
列的字符集最终影响该列中存储的数据的字符集



- 若创建表命令上没有显式设置列字符集或排序规则，则列的字符集继承自表的字符集
- 若创建表命令上有显式设置列字符集或排序规则，则列的字符集为指定的字符集或排序规则

#### 1. 修改指定列的字符集
```sql
alter table table_name modify col1 char(50) character set charset_name;
```

#### 2. 修改表的所有列的字符集
```sql
alter table table_name convert to character set charset_name;
```

#### 3. 修改表的默认字符集
> 不会修改现有列的字符集，仅仅对该表新增的列有作用
```sql
alter table table_name default character set charset_name;
```

**注意**：转换数据存在相关危险，请记住，这些数据转换与修改表的默认字符集不同，后者仅影响更改后添加的任何列的默认字符集


## 五、最佳实践

保持简单
以下是我们迄今为止所学到的内容的方便总结：

1. 变量`character_set_server`影响数据库的创建。

2. 数据库的字符集会影响在该数据库内创建的表。

3. 变量`character_set_database`采用默认数据库（或如果未连接到任何数据库时，设置为`character_set_server`）的值并影响`load data`

4. 表的字符集影响在该表内创建或添加的列。

5. 列的字符集控制数据的字符集。

6. 若修改character_set_server的值、数据库的默认字符集、表的默认字符集，则不会更改已创建对象的默认字符集，也不会转换任何数据。

7. 修改列的字符集是最终影响数据的字符集，修改列并更改其字符集时，会强制 MySQL 转换数据

前面的项目描述了默认值，可以通过在创建数据库/表/列时显式告知字符集和排序规则来推翻默认值。

因此，我们有：`character_set_server` -> 数据库 -> 表 -> 列 -> 数据。

```viz-dot
digraph "character_set_server" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "服务器字符集" -> "数据库字符集";
        "数据库字符集" -> "表字符集";
        "表字符集" -> "列字符集";
        "列字符集" -> "数据";

}
```





## 六、客户端字符集

前面的字符集与数据在数据库中的存储方式有关。

还有其他变量控制如何从数据库发送和检索信息。这四个变量是：
- `character_set_client`
- `character_set_connection`
- `collat​​ion_connection`
- `character_set_results`

前三个与连接到数据库的客户端发送的命令的字符集有关，而character_set_results配置MySQL将用于向客户端发送查询结果的字符集。

变量character_set_client控制连接到数据库的客户端发送的命令的字符集。
'character_set_connection'和'collat​​ion_connection'控制用于字符串比较的字符集和排序规则。

对于where column = 'mytext'的查询，MySQL 将使用连接的'character_set_connection'和'collat​​ion_connection'来解释字符串 'mytext' 。例如：


```sql
[dbscale_internal@172.17.135.83:16310 17:28:32(performance_schema)]$ select * from performance_schema.global_variables where variable_name in ('character_set_connection','collation_connection','character_set_client','character_set_results');
+--------------------------+--------------------+
| VARIABLE_NAME            | VARIABLE_VALUE     |
+--------------------------+--------------------+
| character_set_client     | utf8mb4            |
| character_set_connection | utf8mb4            |
| character_set_results    | utf8mb4            |
| collation_connection     | utf8mb4_0900_ai_ci |
+--------------------------+--------------------+
4 rows in set (0.01 sec)


```


1. 在 MySQL 客户端执行：`SET NAMES utf8mb4;`

2. 在 MySQL 客户端连接时指定字符集，例如：`mysql -u root -p --default-character-set=utf8mb4`

3. 在 MySQL 客户端执行：`SET character_set_client=utf8mb4;`

4. 在 MySQL 配置文件中设置

```sql
[client]
default-character-set=utf8mb4

```
5. 在连接地址配置：`jdbc:mysql://localhost:3306/db?useUnicode=true&characterEncoding=utf8mb4`




`--default-character-set=utf8mb4` 与  `set names utf8mb4` 等价















