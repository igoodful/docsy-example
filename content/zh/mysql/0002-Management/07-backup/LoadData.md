---
title: load data
description: load data local infile
date: 2023-10-12
weight: 20000


---
{{< alert >}}
load data local infile
> 1. MySQL服务器需要开启：local_infile=on;
>
> 2. MySQL客户端需要开启：--local-infile=1
```sql
LOAD DATA LOCAL
    INFILE 'fileName'
    [REPLACE | IGNORE]
    INTO TABLE table_name
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
    ]
    [LINES
        [TERMINATED BY 'string']
    ]
    [IGNORE number {LINES | ROWS}]
    [(column_name_or_user_var
    [, column_name_or_user_var] ...)]
```

{{< /alert >}}

## 配置

```sql
shell> cat my.cnf
[mysql]
local-infile
[mysqld]
local-infile
```

## load data local infile

1. 如果LOCAL未指定，则该文件必须位于服务器主机上。服务器直接读取文件，定位如下：

   - 如果文件名是绝对路径名，服务器将按给定方式使用它。

   - 如果文件名是带有前导组件的相对路径名，服务器将查找相对于其数据目录的文件。

   - 如果文件名没有前导组件，服务器将在默认数据库的数据库目录中查找该文件。

2. 如果LOCAL指定，则该文件必须位于客户端主机上。客户端程序读取该文件，并按如下方式定位它：

   - 如果文件名是绝对路径名，则客户端程序按给定方式使用它。

   - 如果文件名是相对路径名，则客户端程序将查找相​​对于其调用目录的文件。





{{<alert title="参数详解" color="secondary">}}

- fileName：文件名，若file_name使用相对路径，则是相对于客户端程序启动时的路径；建议使用绝对地址

- replace into table tableName ：导入数据时，遇到主键重复则强制用当前数据覆盖已有数据。

- ignore into table tableName ：导入数据时，遇到主键重复或者数据问题时则自动忽略失败行，部分行导入失败但不影响其他行导入。

- fields terminated by 'string'：定义列之间的分隔符，默认为\t

- fields enclosed by 'char'：定义每列数据的enclosed符号，例如，某一列数据为"a"，定义enclosed by '"'后，导入数据时先将"a"前后的"移除，然后导入数据。

- lines terminated by 'string'：定义每行之间的分隔符，默认为\n。widnows下用 "\r\n"

- ignore xxx lines：设置导入数据时忽略开始的某几行。例如IGNORE 10 LINES，导入数据时忽略前10行数据，即前10行数据不会导入MySQL

- (column_name_or_user_var,...)：设置导入的列，如果不设置，默认按照列的顺序来导入数据。如果导入列数 > 目标表的列数，则系统自动忽略多余的列。如果导入列数 < 目标表的列数，则后续缺少的列将自动填充默认值。


{{</alert>}}





### 举例说明
```sql
mysql -uroot
-p'123456'
-h172.17.138.54
-P3306
-Dapple
--local-infile=1
-e "load data local infile '/tmp/result.text' replace into table tb_user lines terminated by '\n' (id,k,c,pad);"
```

## 常见问题处理


### 1. Loading local data is disabled; this must be enabled on both the client and server sides
{{<alert title="问题" color="secondary">}}
MySQL 8.0 在导入数据时，需要在服务端开启：local_infile=on

- 在mysql服务端配置文件中添加 local_infile=on 配置即可，

- 也可在线动态修改该参数
{{</alert>}}
```sql
[root@k8s-node-54 tmp]# mysql -uadmin
-p'123456'
-h172.17.138.54
-P16315
--local-infile=1
-Dapple
-e "load data local infile '/tmp/sbtest3.txt' replace into table sbtest5 lines terminated by '\n' (id,k,c,pad);"

mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 3948 (42000) at line 1: Loading local data is disabled; this must be enabled on both the client and server sides

mysql> show variables like 'local_infile';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| local_infile  | OFF   |
+---------------+-------+
1 row in set (0.00 sec)

mysql> set global local_infile=on;
Query OK, 0 rows affected (0.00 sec)

```

### 2. LOAD DATA LOCAL INFILE file request rejected due to restrictions on access
{{<alert title="问题" color="secondary">}}
MySQL 8.0 在导入数据时，需要在客户端开启：--local-infile=1

故在mysql客户端上添加参数 --local-infile=1 即可

{{</alert>}}
```sql
[root@k8s-node-54 tmp]# mysql -uadmin
-p'123456'
-h172.17.138.54
-P16315
-Dapple
-e "load data local infile '/tmp/sbtest3.txt' replace into table sbtest5 lines terminated by '\n' (id,k,c,pad);"

mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 2068 (HY000) at line 1: LOAD DATA LOCAL INFILE file request rejected due to restrictions on access.

[root@k8s-node-54 tmp]# mysql -uadmin
-p'123456'
-h172.17.138.54
-P16315
--local-infile=1
-Dapple
-e "load data local infile '/tmp/sbtest3.txt' replace into table sbtest5 lines terminated by '\n' (id,k,c,pad);"

mysql: [Warning] Using a password on the command line interface can be insecure.

```























