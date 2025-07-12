---
title: 添加从库节点
date: 2023-10-24
description: dbscale dynamic


weight: 70000
---

#### 正常流程是

> 区别灾备的操作

```sql
dbscale dynamic remove slave normal_0_2 from normal_0;
dbscale dynamic remove dataserver normal_0_2;
```

#### 添加：

```sql
dbscale dynamic add dataserver normal_0_2;
dbscale dynamic add slave normal_0_2-10-100-400-800 to normal_0;
```








