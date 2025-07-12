---
title: include
description: 头文件
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}


{{%/pageinfo%}}



{{<note>}}
<!---->
include
{{</note>}}


### 安装
```bash
gcc -Wall -c -fPIC test_shared.c

gcc -shared -fPIC test_shared.o -o libtest_shared.so
```


## 查看安装结果
```sql


```
