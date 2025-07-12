---
title: socat
date: 2023-10-12
weight: 20000
description: >
  Socat是Socket CAT的简写， 是Linux下的一个多功能的网络工具.


---

{{< alert >}}

{{< /alert >}}


## 一、安装socat

### （1）源码安装
```bash
#!/bin/bash
wget http://www.dest-unreach.org/socat/download/socat-1.7.3.2.tar.gz
tar -xzvf socat-1.7.3.2.tar.gz
cd socat-1.7.3.2
./configure
make
make install

```
### （2）yum安装

```bash
yum install -y socat
```

## 二、使用




### 文件传输
- -u 表示数据传输模式为单向，从左面参数到右面参数。

1. 将文件filename.tgz从10.10.10.11传送到上10.10.10.22
```bash

socat -u open:filename.tgz tcp-listen:2000,reuseaddr # 先在10.10.10.11上执行


socat -u tcp:10.10.10.11:2000 open:filename.tgz,create # 后在10.10.10.22上执行
```

2. 将文件filename.tgz从10.10.10.11传送到10.10.10.22上
```bash

socat -u TCP-LISTEN:18080 open:filename.tgz,create # 文件接受端先开启，在10.10.10.22上执行


socat -u open:filename.tgz TCP:10.10.10.22:18080 # 文件发送端后开启，在10.10.10.11上执行
```



3. 将文件filename.tgz从10.10.10.11传送到10.10.10.22上

```bash
socat TCP4-LISTEN:12345,reuseaddr - | cat - > filename  # 先在文件接受端先开启，10.10.10.22上执行

echo "hello" |socat - TCP4:10.10.10.22:12345,retry=100  # 再在文件发送端后开启，10.10.10.11上执行

cat filename |socat - TCP4:10.10.10.22:12345,retry=100  # 再在文件发送端后开启，10.10.10.11上执行

```




