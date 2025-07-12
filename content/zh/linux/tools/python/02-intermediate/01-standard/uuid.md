---
title: uuid
date: 2023-09-30
weight: 8500
description: >
  uuid 学习.
---
{{< alert >}}
在应用程序中使用UUID生成唯一的数据库密钥



{{< /alert >}}

### 安装 uuid

```bash
pip install uuid
```



### 使用 uuid

```python
import uuid

print(uuid.uuid1()) # 根据主机ID，序列号和当前时间生成UUID。它使用主机的MAC地址作为唯一性来源。注意：uuid1是不安全的，因为它以UUID显示计算机的网络地址，因此存在隐私问题。

print(uuid.uuid4())  # 基于随机数

print(uuid.uuid3("namespace", "name")) # 计算命名空间和名字的MD5散列值来生成UUID,

print(uuid.uuid5("namespace", "name")) # 计算命名空间和名字的SHA-1散列值来生成UUID


```























