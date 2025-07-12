---
title: pkg-config
description: pkg-config
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [libtool 官网](https://mirrors.ocf.berkeley.edu/gnu/libtool/)

{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://mirrors.ocf.berkeley.edu/gnu/libtool/libtool-2.4.tar.gz
tar -xzvf libtool-2.4.tar.gz && cd libtool-2.4
./configure --prefix=$HOME/local --with-internal-glib && make && make install
```


## 查看安装结果
```sql
which autoconf
autoconf --version
```
