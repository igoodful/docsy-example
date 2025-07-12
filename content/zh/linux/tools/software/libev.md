---
title: libev
description: libev
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

[libev 官网](http://dist.schmorp.de/libev/)

[libev 官网](https://fra.de.distfiles.macports.org/libev/)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget http://dist.schmorp.de/libev/libev-4.33.tar.gz
tar -xzvf autoconf-2.72.tar.gz && cd autoconf-2.72
./configure --prefix=$HOME/local/ && make && make install
```


## 查看安装结果
```sql
which autoconf
autoconf --version
```
