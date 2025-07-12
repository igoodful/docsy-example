---
title: libaio
description: libaio
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [libaio 官网](https://pagure.io/libaio)
- [libaio github](https://github.com/crossbuild/libaio)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc
{{</note>}}


### 安装
```bash
git clone https://pagure.io/libaio.git
cd libaio
make prefix=/root/local/libaio
make install prefix=/root/local/libaio
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
