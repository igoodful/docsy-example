---
title: mysql shell
description: mysql shell
date: 2023-10-12
weight: 20000


---

{{< alert >}}

MySQL Shell 8.0.21 增加了一种新的逻辑备份恢复方法，有更快的备份恢复效率，支持zstd实时压缩，支持分块并行导出，load data并行导入，还能备份到OCI的对象存储。

- util.dumpInstance() 用于备份整个实例
- util.dumpSchemas() 用于备份指定schema
- util.loadDump() 用于恢复备份

做了个对比测试，在零负载下mysql配置参数不变，备份/恢复相同 schema，其中混合了大表和小表，看下这几种方式的实际效果如何。

{{< /alert >}}






## 备份

#### 备份整个实例
```bash
util.dumpInstance()
```


#### 备份指定数据
```bash
util.dumpSchemas(["test"],"test1")
```



## 恢复
```bash
util.loadDump("test1")
```






























