---
title: libicu
description: libicu
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

[libicu 官网](https://icu.unicode.org/home)
[libicu github](https://github.com/unicode-org/icu/tags)
- ICU4C：C，C++
C 和 C++ 语言以及许多操作系统环境不提供对 Unicode 和符合标准的文本处理服务的完全支持。尽管某些平台确实提供了良好的 Unicode 文本处理服务，但可移植应用程序代码无法使用它们。ICU4C 库填补了这一空白。ICU4C 为应用程序提供了开放、灵活、可移植的基础，以满足其软件全球化要求。ICU4C 密切跟踪行业标准，包括 Unicode 和 CLDR（通用区域设置数据存储库）。
- ICU4J：java
{{%/pageinfo%}}



{{<note>}}
<!---->
ICU4C 需要 高版本gcc，gcc-4.8.5肯定不行
{{</note>}}


### 安装
```bash
wget https://github.com/unicode-org/icu/archive/refs/tags/release-74-2.tar.gz
tar -xzf release-74-2.tar.gz && cd icu-release-74-2/icu4c/source
./configure --prefix=$HOME/local && make && make install
```












## 查看安装结果
```sql
[root@k8s-node-76 local]# tree libaio
libaio
├── include
│   └── libaio.h
└── lib
    ├── libaio.a
    ├── libaio.so -> libaio.so.1.0.2
    ├── libaio.so.1 -> libaio.so.1.0.2
    └── libaio.so.1.0.2

2 directories, 5 files

```
