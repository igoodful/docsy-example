---
title: skip_networking
description: skip_networking
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## 原理

我们在命令行中指定启动选项时需要在选项名前加上 -- 前缀。另外，如果选项名是由多个单词构成的，它们之间可以由短划线 - 连接起来，也可以使用下划线 _ 连接起来，也就是说 skip-networking 和skip_networking 表示的含义是相同的。


skip_networking 控制 MySQL 服务是否允许 TCP/IP 连接，默认是关闭。如果开启这个变量，MySQL 服务只允许本地连接，不允许任何 TCP/IP 连接。

当配置了 --skip-grant-tables 变量之后，skip_networking 变量默认也会开启，此时禁用任何远程连接。

mysqld --skip-networking


mysqld --skip_networking

mysqld --default-storage-engine=MyISAM

