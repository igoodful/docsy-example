---
title: 修改字符集和排序规则
description: character，collate
date: 2023-10-10
weight: 80000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert >}}

{{< /alert >}}



## 一、修改系统默认字符集和排序规则




### 1. 修改每个mysql实例的排序规则：

```sql
set global collation_connection='utf8mb4_bin';
set global collation_database='utf8mb4_bin';
set global collation_server='utf8mb4_bin';
show global variables like 'collation%';

```


### 2. 持久化参数到配置文件

```sql
collation_server=utf8mb4_bin

```


## 二、修改数据库的字符集和排序规则

```sql
alter database database_name character set utf8mb4 collate utf8mb4_bin;

```

## 三、修改表的字符集和排序规则

### 1. 它只修改表新增列的默认定义
```sql
alter table table_name character set utf8mb4 collate utf8mb4_bin;

```

### 2. 同时修改表字符集和已有列字符集，并将已有数据进行字符集编码转换
```sql
alter table table_name convert to character set utf8mb4 collate utf8mb4_bin;

```



## 四、修改列的字符集和排序规则


```sql
alter table table_name change column_name column_name varchar(64) character set utf8mb4 collate utf8mb4_bin not null;

```




