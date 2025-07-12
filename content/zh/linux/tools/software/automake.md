---
title: automake
description: automake
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [automake 官网](https://ftp.gnu.org/gnu/automake/)
- [automake](https://mirrors.kernel.org/gnu/automake/)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://ftp.gnu.org/gnu/automake/automake-1.16.4.tar.gz
tar -xzvf automake-1.16.4.tar.gz && cd automake-1.16.4
./configure --prefix=$HOME/local/ && make && make install
```


## 查看安装结果
```sql
which automake
automake  --version
```
