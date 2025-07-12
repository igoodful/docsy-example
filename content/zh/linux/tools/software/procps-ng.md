---
title: procps-ng
description: procps-ng
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [procps-ng github](https://github.com/cdown/procps-ng)
- [procps-ng gitlab](https://gitlab.com/procps-ng/procps/)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://ftp.gnu.org/gnu/autoconf/autoconf-2.72.tar.gz
tar -xzvf autoconf-2.72.tar.gz && cd autoconf-2.72
./configure --prefix=$HOME/local/ && make && make install
```


## 查看安装结果
```sql
which autoconf
autoconf --version
```
