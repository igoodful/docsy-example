---
title: bashrc
description: bashrc
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### `cat $HOME/.bashrc`
> PATH
> CC
> CXX
> PKG_CONFIG_PATH
> CPLUS_INCLUDE_PATH
> C_INCLUDE_PATH
> LIBRARY_PATH
> LD_LIBRARY_PATH
```bash
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi

export PATH=$HOME/local/bin:$PATH
export CC=$HOME/local/bin/gcc
export CXX=$HOME/local/bin/g++

export PKG_CONFIG_PATH=$HOME/local/lib/pkgconfig
export CPLUS_INCLUDE_PATH=$HOME/local/include
export C_INCLUDE_PATH=$HOME/local/include

export LIBRARY_PATH=$HOME/local/lib64:$HOME/local/lib6
export LD_LIBRARY_PATH=$HOME/local/lib64:$HOME/local/lib
```


## 查看安装结果
```sql

```
