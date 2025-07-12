---
title: params
description: params
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>




| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |


{{<alert>}}

- `innobase_hton` 对象初始化
- 初始化`innodb`参数：`innodb_init_params`

{{</alert>}}



### MYSQL_SYSVAR_LONG
```c++
#define DECLARE_MYSQL_SYSVAR_SIMPLE(name, type) \
  struct {                                      \
    MYSQL_PLUGIN_VAR_HEADER;                    \
    type *value;                                \
    type def_val;                               \
    type min_val;                               \
    type max_val;                               \
    type blk_sz;                                \
  } MYSQL_SYSVAR_NAME(name)

#define MYSQL_SYSVAR_LONG(name, varname, opt, comment, check, update, def, \
                          min, max, blk)                                   \
  DECLARE_MYSQL_SYSVAR_SIMPLE(name, long) = {                              \
      PLUGIN_VAR_LONG | ((opt)&PLUGIN_VAR_MASK),                           \
      #name,                                                               \
      comment,                                                             \
      check,                                                               \
      update,                                                              \
      &varname,                                                            \
      def,                                                                 \
      min,                                                                 \
      max,                                                                 \
      blk}

static MYSQL_SYSVAR_LONG(
    open_files, innobase_open_files, PLUGIN_VAR_RQCMDARG | PLUGIN_VAR_READONLY,
    "How many files at the maximum InnoDB keeps open at the same time.",
    nullptr, nullptr, 0L, 0L, LONG_MAX, 0);

typedef DECLARE_MYSQL_SYSVAR_SIMPLE(sysvar_long_t, long);
```



### 解析
```c++
#define MYSQL_SYSVAR_LONG(name, varname, opt, comment, check, update, def, \
                          min, max, blk)                                   \
    struct {                                      \
    MYSQL_PLUGIN_VAR_HEADER;                    \
    long *value;                                \
    long def_val;                               \
    long min_val;                               \
    long max_val;                               \
    long blk_sz;                                \
  } MYSQL_SYSVAR_NAME(name) = {                              \
      PLUGIN_VAR_LONG | ((opt)&PLUGIN_VAR_MASK),                           \
      #name,                                                               \
      comment,                                                             \
      check,                                                               \
      update,                                                              \
      &varname,                                                            \
      def,                                                                 \
      min,                                                                 \
      max,                                                                 \
      blk}


#define DECLARE_MYSQL_SYSVAR_SIMPLE(name, long) \










```































