---
title: 修改表结构
description: alter table
date: 2023-10-12
weight: 60000


---


### mysql_config_editor对应的参数信息如下：




## 一、库管理


#### 1. 修改库的字符集和排序规则
```sql
[root@127.0.0.1:8032 09:54:36((none))]$ show create database sbtest\G
*************************** 1. row ***************************
       Database: sbtest
Create Database: CREATE DATABASE `sbtest` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.01 sec)

[root@127.0.0.1:8032 09:54:54((none))]$ alter database sbtest  character set utf8mb4 collate utf8mb4_bin;
Query OK, 1 row affected (0.03 sec)

[root@127.0.0.1:8032 09:55:26((none))]$ show create database sbtest\G
*************************** 1. row ***************************
       Database: sbtest
Create Database: CREATE DATABASE `sbtest` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_bin */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)

[root@127.0.0.1:8032 09:55:29((none))]$

```









## 二、表管理

#### create table  t like xxx
```bash
create table  t like xxx;
insert into t select * from xxx;

-- 在mysql 8中只会生成一个gtid
create table t as select * from xxx;

show binlog events in 'binlog.000001'\G
```

#### create temporary table t
```bash
begin;
create temporary table t(id int,log_date date);
insert t values(100,'2024-04-18');
commit;
show binlog events in 'binlog.000001'\G
```




#### 修改表名称

```sql
alter table t rename to xxx;
```

#### 修改字符集和排序规则，对现有数据不生效

```sql
alter table t character set utf8mb4 collate utf8mb4_bin;;
```

#### 修改字符集和排序规则，对现有数据生效
```sql
alter table t convert to character set utf8mb4 collate utf8mb4_bin;
```



## 三、列管理

#### 添加列

```sql
alter table t add column xxx varchar(255) not null default '';
```

#### 删除列
```sql
alter table t drop column xxx;
```

#### 修改列名称
```sql
alter table t change column old_column new_column varchar(255) ;

```


#### 修改列属性
```sql
alter table t modify column xxx varchar(255) ;
```


#### 添加备注

```sql
alter table t comment '注释';
```

#### 修改字段默认值

```sql
alter table t alter column xxx set default  默认值;
```

#### 修改字段的字符集和排序规则

```sql
alter table t change column_name column_name varchar(64) character set utf8mb4 collate utf8mb4_bin not null;
```













## 四、索引管理

#### 1. 添加主键索引

```sql
alter table t add primary key (column_name);

```

#### 2. 添加唯一索引

```sql
alter table t add unique (column_name);

```

#### 3. 添加普通索引
```sql
alter table t add index (xxx);

```

#### 4. 添加全文索引
```sql
alter table t add fulltext (xxx);

```

#### 5. 添加外键
```sql
alter table t add constraint fk_name foreign key (column_name) references parent_table (column_name);
```

#### 6. 删除索引
```sql
alter table t drop primary key;

alter table t drop index xxx;
```


#### 7. 查看索引

```sql
show index from t;
```



## 五、分区管理

分区类型：
- RANGE分区：基于列的值范围将数据分配到不同的分区。例如，可以根据日期范围将数据分配到不同的月份或年份的分区中。
- LIST分区：类似于RANGE分区，但LIST分区是基于列的离散值集合来分配数据的。可以指定一个枚举列表来定义每个分区的值。
- HASH分区：基于用户定义的表达式的哈希值来分配数据到不同的分区。这种分区方式适用于确保数据在各个分区之间均匀分布。
- KEY分区：类似于HASH分区，但KEY分区支持计算一列或多列的哈希值来分配数据。它支持多列作为分区键，并且提供了更好的数据分布和查询性能。


实施MySQL分区需要仔细规划和设计。以下是一些建议的步骤：

1. **确定分区键**：选择一个合适的列作为分区键，该列的值将用于将数据分配到不同的分区中。通常选择具有连续值或离散值的列作为分区键。
2. **选择合适的分区类型**：根据数据的特点和查询需求选择合适的分区类型（RANGE、LIST、HASH或KEY）。确保所选的分区类型能够均匀地分布数据并提高查询性能。
3. 创建分区表：使用CREATE TABLE语句创建分区表，并指定分区键和分区类型等参数。例如，使用RANGE分区类型创建一个按月分区的销售数据表





#### 1. range


```sql
create table orders
    (
        orderkey      int(11) not null           ,
        custkey       int(11) not null           ,
        orderstatus   char(1) default null       ,
        totalprice    decimal(10,2) default null ,
        order_date     date not null              ,
        orderpriority char(15) default null      ,
        clerk         char(15) default null      ,
        shippriority  int(11) default null       ,
        comment       varchar(79) default null   ,
        primary key (orderkey,order_date,custkey)
    )
partition by range (to_days(order_date)) (partition item1 values less than (to_days('2000-01-01')), partition item2 values less than (to_days('2020-01-01')),partition item3 values less than (maxvalue));

```

删除分区
```sql
ALTER TABLE tr DROP PARTITION p2;
```


#### 2. list


###### A. list
> 分区键必须为主键的一部分
>
> 分区字段只支持int数据类型

```sql
create table my_list
    (
        employee_id    int(6)            ,
        first_name     varchar(20)       ,
        last_name      varchar(25)       ,
        email          varchar(25)       ,
        phone_number   varchar(20)       ,
        hire_date      datetime          ,
        job_id         varchar(10)       ,
        salary         int(8)            ,
        commission_pct decimal(2,2)      ,
        manager_id     decimal(6,0)      ,
        department_id  int(4)            ,
        primary key(employee_id)
    )
partition by
list(employee_id) ( partition p0 values in (101,103,105,107,109), partition p1 values in (102,104,106,108,110) );


create table my_list
    (
        employee_id    int(6)       ,
        first_name     varchar(20)  ,
        last_name      varchar(25)  ,
        email          varchar(25)  ,
        phone_number   varchar(20)  ,
        hire_date      datetime     ,
        job_id         varchar(10)  ,
        salary         int(8)       ,
        commission_pct decimal(2,2) ,
        manager_id     decimal(6,0) ,
        department_id  int(4)       ,
        primary key(employee_id,department_id)
    )
partition by
list(department_id) ( partition p0 values in (101,103,105,107,109), partition p1 values in (102,104,106,108,110) );
```


###### B. list columns
> 分区键必须为主键的一部分
>
> 分区字段支持使用INT、CHAR、VARCHAR、DATE等数据类型

```sql
create table list_columns
    (
        employee_id    int(6)      ,
        first_name     varchar(20) ,
        last_name      varchar(25) ,
        email          varchar(25) ,
        phone_number   varchar(20) ,
        hire_date      datetime    ,
        job_id         varchar(10) ,
        salary         int(8)      ,
        commission_pct decimal(2,2),
        manager_id     decimal(6,0),
        department_id  int(4),
	primary key(employee_id,last_name)
    )
partition by
list columns(last_name) ( partition p0 values in ('app','ppa'), partition p1 values in ('bba','abb') );


create table list_columns
    (
        employee_id    int(6)      ,
        first_name     varchar(20) ,
        last_name      varchar(25) ,
        email          varchar(25) ,
        phone_number   varchar(20) ,
        hire_date      datetime    ,
        job_id         varchar(10) ,
        salary         int(8)      ,
        commission_pct decimal(2,2),
        manager_id     decimal(6,0),
        department_id  int(4),
	primary key(employee_id,last_name)
    )
partition by
list columns(employee_id) ( partition p0 values in (1,2), partition p1 values in (3,4) );
```

#### 3. hash
> 分区键必须为主键的一部分

```sql
create table sales_hash
    (
        id        int not null           ,
        sale_date date not null          ,
        amount    decimal(10, 2) not null,
        primary key (id, sale_date)
    )
partition by hash(id) partitions 4;


create table sales_hash
    (
        id        int not null           ,
        sale_date date not null          ,
        amount    decimal(10, 2) not null,
        primary key (id, sale_date)
    )
partition by hash(year(sale_date)) partitions 4;

```


#### 4. key
> 分区键必须为主键的一部分

```sql
create table sales_key
    (
        id        int not null           ,
        sale_date date not null          ,
        amount    decimal(10, 2) not null,
        primary key (id, sale_date)
    )
partition by key(id) partitions 4;


create table sales_key
    (
        id        int not null           ,
        sale_date date not null          ,
        amount    decimal(10, 2) not null,
        primary key (id, sale_date)
    )
partition by key(sale_date) partitions 4;
```




















