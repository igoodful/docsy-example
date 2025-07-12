---
title: define
description: 文本替换
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}


{{<alert>}}


{{</alert>}}
---
&#128311;【】2017年上半年，城镇居民人均可支配收入18332元，增长8.1%（以下如无特别说明，均为同比名义增长）

2016年上半年，城镇居民人均可支配收入约为多少元？

【解析】$\cfrac{18332}{ 1 + 0.081 } = 17000$



## 标识符连接：\#\#

`cat /root/mysql-server-8032/include/mysql/plugin.h`
```c++
#define mysql_declare_plugin(NAME)                                        \
  __MYSQL_DECLARE_PLUGIN(NAME, builtin_##NAME##_plugin_interface_version, \
                         builtin_##NAME##_sizeof_struct_st_plugin,        \
                         builtin_##NAME##_plugin)
```






##



```c++
#ifndef MYSQL_DYNAMIC_PLUGIN
#define __MYSQL_DECLARE_PLUGIN(NAME, VERSION, PSIZE, DECLS)         \
  MYSQL_PLUGIN_EXPORT int VERSION = MYSQL_PLUGIN_INTERFACE_VERSION; \
  MYSQL_PLUGIN_EXPORT int PSIZE = sizeof(struct st_mysql_plugin);   \
  MYSQL_PLUGIN_EXPORT struct st_mysql_plugin DECLS[] = {
#else
#define __MYSQL_DECLARE_PLUGIN(NAME, VERSION, PSIZE, DECLS)  \
  MYSQL_PLUGIN_EXPORT int _mysql_plugin_interface_version_ = \
      MYSQL_PLUGIN_INTERFACE_VERSION;                        \
  MYSQL_PLUGIN_EXPORT int _mysql_sizeof_struct_st_plugin_ =  \
      sizeof(struct st_mysql_plugin);                        \
  MYSQL_PLUGIN_EXPORT struct st_mysql_plugin _mysql_plugin_declarations_[] = {
#endif
```



## 定义函数指针别名

```c++
typedef int (*commit_t)(handlerton *hton, THD *thd, bool all);

typedef int (*rollback_t)(handlerton *hton, THD *thd, bool all);

typedef int (*prepare_t)(handlerton *hton, THD *thd, bool all);

typedef int (*recover_t)(handlerton *hton, XA_recover_txn *xid_list, uint len,
                         MEM_ROOT *mem_root);
```





## 定义简单类型

```c++
typedef unsigned char byte;


```

## 定义结构体别名


```c++
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} Book;
```


- typedef 仅限于为类型定义符号名称，#define 不仅可以为类型定义别名，也能为数值定义别名，比如您可以定义 1 为 ONE。
- typedef 是由编译器执行解释的，#define 语句是由预编译器进行处理的。



















