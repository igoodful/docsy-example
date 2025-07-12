---
title: mysql如何通过单个ibd文件进行数据恢复
date: 2023-10-06
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.


weight: 60000
---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


####################################

需求场景：

       业务误操作时刻在路上，那么数据恢复是件常见的事，我们通常通过innobackupex或xtrabackup进行数据库实例的全量备份，遇到需要进行数据恢复时，我们就将该备份拷贝到测试机上进行恢复，这对于一般的中小型数据库实例而言，拷贝数据花不了多少时间，还是很方便的，

但是当数据库实例的数据量很大时，这个时候拷贝几百G，甚至几个T的数据，则是非常耗时。那么能不能只拷贝误操作的那几张表的数据呢，答案是可以，通过拷贝误操作的那几张表的ibd文件即可完成，下面就通过ibd文件进行数据恢复。[前提是数据库实例开启了每张表都是一个独立表空间，即查看这个全局变量：innodb_file_per_table=1]



假设线上集群的实例为10.10.10.11:3306，备份机所在机器为10.10.10.22，业务误操作了线上apple库的ipad表，测试机的mysql实例为10.10.10.33:3306



1，登录10.10.10.11:3306，查看apple.ipad表的表结构,同时预估一下数据量：
```sql
mysql> show create table apple.ipad \G;

mysql> select count(*)  from apple.ipad ;
```


2，在测试机实例10.10.10.33:3306上创建相同的库和表，即创建apple和ipad表：

```sql
mysql> create  database apple;

mysql> use apple;
mysql> create table ipad (id bigint(20) NOT NULL AUTO_INCREMENT,name varchar(255) NOT NULL,address varchar(255) NOT NULL,blob_name blob,PRIMARY KEY (id),KEY idx_name (name)) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4;
--- # 执行后会在apple数据库目录下面新增两个文件：ipad.ibd和ipad.frm文件

mysql> alter table ipad discard tablespace;

--- 执行后，该实例的apple库的ipad表只剩下数据定义文件ipad.frm，表空间文件ipad.ibd已被删除
```






3，拷贝备份机10.10.10.22上备份对应的表空间文件apple/ipad.ibd到测试机10.10.10.33:3306的apple数据库目录下：
复制代码
## 任意地方均可执行，网络通即可：
```bash
#!/bin/bash
# echo work@10.10.10.22:/home/work/2021/data/apple/ipad.ibd   work@10.10.10.33:/home/work/mysql_3306/data/apple/
scp  work@10.10.10.22:/home/work/2021/data/apple/ipad.ibd   work@10.10.10.33:/home/work/mysql_3306/data/apple/
```
## 在测试机10.10.10.33上执行：

```bash
#!/bin/bash
chown -R work:work   /home/work/mysql_3306
```

## 如果是拷贝正在运行的实例上的ibd文件则需要在该实例上执行：
```sql
 mysql> flush tables;lock tables apple.ipad  read;
```





4，登录测试机10.10.10.33::3306导入表空间数据：
```sql
mysql> alter table apple.ipad  import tablespace;
```


 5，经过上面4个步骤已经将误操作的表的备份加载到了测试机上的数据库实例中，如果还需要将其恢复到该备份时刻后面的任意一刻，则需要通过配置复制过滤到线上集群的实例，且线上集群的某个实例在该备份时刻后到现在的的binlog还存在，这个步骤就省略了
