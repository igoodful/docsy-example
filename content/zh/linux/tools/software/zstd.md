---
title: zstd
description: zstd
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

[zstd github](https://github.com/facebook/zstd)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://github.com/facebook/zstd/archive/refs/tags/v1.5.5.tar.gz
tar -xzvf autoconf-2.72.tar.gz && cd autoconf-2.72
./configure --prefix=$HOME/local/ && make && make install
```


## 查看安装结果
```sql
which autoconf
autoconf --version
```
