---
title: 基础规范
description: 基础规范
date: 2023-10-06
weight: 60000


---

{{< alert >}}
基础规范
{{< /alert >}}

### 基础规范

{{< alert title="配置信息" color="secondary">}}

| 硬件名称                 | 配置             |
|:-------------------------|:-----------------|
| Engine                   | InnoDB           |
| 字符集                   | UTF8MB4          |
| lower_case_table_names=1 | 表名大小写不敏感 |
| 事务隔离级别             | READ-COMMITED    |


{{< /alert >}}



{{< alert title="对象规范" color="secondary">}}
1. 数据库名：db_databaseName

2. 表名：tbl_tableName

3. 临时表: tmp_tableName

4. 视图名：v_viewName

5. 主键名：pk_tableName_columnName

6. 唯一索引名：udx_columnName

7. 普通索引名：idx_columnName
{{< /alert >}}





