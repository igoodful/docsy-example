---
title: mydumper
description: A
date: 2023-10-12
weight: 20000


---

{{< alert >}}

1. 最快（逻辑备份中）

2. 适用于数据量大于50G

3. 支持where

4. 有TFWRL锁

{{< /alert >}}






## 备份
```bash
/usr/bin/time mydumper -u msandbox -p msandbox -h 127.0.0.1 -P 8021 -B test -t 4 -v 3 -c -o dumper
```



## 恢复
```bash
/usr/bin/time myloader -u msandbox -p msandbox -h 127.0.0.1 -P 8021 -B test -t 4 -v 3  -d dumper/
```






























