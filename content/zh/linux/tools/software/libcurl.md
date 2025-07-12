---
title: libcurl
description: libcurl
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [curl 官网](https://curl.se/download.html)
- [curl github](https://github.com/curl/curl/tags)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://github.com/curl/curl/archive/refs/tags/curl-8_5_0.tar.gz
tar -xzvf curl-8_5_0.tar.gz && cd curl-8_5_0
./configure --prefix=$HOME/local/ && make && make install
```


## 查看安装结果
```sql
which autoconf
autoconf --version
```
