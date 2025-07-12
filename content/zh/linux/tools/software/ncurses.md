---
title: ncurses
description: ncurses
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

[ncurses 官网](https://ftp.gnu.org/gnu/ncurses/)
{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc
{{</note>}}


### 安装
```bash
wget https://ftp.gnu.org/gnu/ncurses/ncurses-6.4.tar.gz
tar -xzvf ncurses-6.4.tar.gz && cd ncurses-6.4
./configure --with-shared --with-normal  --prefix=$HOME/local/ncurses-6.4 && make -j4 && make install
```


## 查看安装结果
```sql
[root@k8s-node-76 local]# cd $HOME/local
[root@k8s-node-76 local]# tree ncurses-6.4
ncurses-6.4
├── bin
│   ├── captoinfo -> tic
│   ├── clear
│   ├── infocmp
│   ├── infotocap -> tic
│   ├── ncurses6-config
│   ├── reset -> tset
│   ├── tabs
│   ├── tic
│   ├── toe
│   ├── tput
│   └── tset
├── include
│   └── ncurses
│       ├── cursesapp.h
│       ├── cursesf.h
│       ├── curses.h
│       ├── cursesm.h
│       ├── cursesp.h
│       ├── cursesw.h
│       ├── cursslk.h
│       ├── eti.h
│       ├── etip.h
│       ├── form.h
│       ├── menu.h
│       ├── nc_tparm.h
│       ├── ncurses_dll.h
│       ├── ncurses.h -> curses.h
│       ├── panel.h
│       ├── termcap.h
│       ├── term_entry.h
│       ├── term.h
│       ├── tic.h
│       └── unctrl.h
├── lib
│   ├── libform.a
│   ├── libform_g.a
│   ├── libform.so -> libform.so.6
│   ├── libform.so.6 -> libform.so.6.4
│   ├── libform.so.6.4
│   ├── libmenu.a
│   ├── libmenu_g.a
│   ├── libmenu.so -> libmenu.so.6
│   ├── libmenu.so.6 -> libmenu.so.6.4
│   ├── libmenu.so.6.4
│   ├── libncurses.a
│   ├── libncurses++.a
│   ├── libncurses_g.a
│   ├── libncurses++_g.a
│   ├── libncurses.so -> libncurses.so.6
│   ├── libncurses.so.6 -> libncurses.so.6.4
│   ├── libncurses.so.6.4
│   ├── libpanel.a
│   ├── libpanel_g.a
│   ├── libpanel.so -> libpanel.so.6
│   ├── libpanel.so.6 -> libpanel.so.6.4
│   ├── libpanel.so.6.4
│   └── terminfo -> ../share/terminfo
└── share

```
