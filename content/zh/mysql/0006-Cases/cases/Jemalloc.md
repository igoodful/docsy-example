---
title: 更换MySQL内存分配器
date: 2023-10-20
description: 内存分配器版本问题.
weight: 60000
---

{{< alert >}}

解决MySQL占用内存只增不减问题，需要正确的内存分配器与内存分配器版本

{{< /alert >}}

### 一、安装jemalloc

{{< alert title="注意" color="secondary">}}
一定要核对版本信息和md5值
{{< /alert >}}
```bash
git clone https://github.com/jemalloc/jemalloc

cd jemalloc

./autogen.sh --prefix=/xx/xxx

make && make install

```

### 二、配置jemalloc

```sql
chmod 755 /usr/local/lib/libjemalloc.so

# cat /home/work/mysql_3306/etc/my.cnf
[mysqld_safe]
malloc-lib=/usr/local/lib/libjemalloc.so

# 或者用如下命令修改
sed  -i.bak '4i\[mysqld_safe]\nmalloc-lib=/usr/local/lib/libjemalloc.so' /home/work/mysql_3306/etc/my.cnf

```

### 三、关闭numa、关闭透明大页、设置hugepagesz

```sql

sed -i 's/quiet/quiet numa=off transparent_hugepage=never default_hugepagesz=2M hugepagesz=2M /' /etc/default/grub

grub2-mkconfig -o /boot/grub2/grub.cfg

grub2-mkconfig -o /etc/grub2-efi.cfg

```


### 四、关闭实例，重启机器，核对变更

```sql

# "重启"

reboot

# "查看numa是否关闭"

numactl --hardware

# "查看透明大页是否关闭"

cat /sys/kernel/mm/transparent_hugepage/enabled

# "查看巨页是否为2MB"

cat /proc/meminfo |grep -i huge


# "核对MySQL是否使用了libjemalloc"

lsof -p `pidof mysqld` | grep -i libjemalloc


```




### 一、安装tcmalloc
```sql
yum -y install gcc make gcc-c++ libunwind

git https://github.com/gperftools/gperftools

cd gperftools

./autogen.sh

./configure

make && make install

```

### 二、配置
```sql
[mysqld-safe]
malloc-lib=/usr/local/lib/tcmalloc.so
```

### 三、重启MySQL
```sql
[mysqld-safe]
malloc-lib=/usr/local/lib/tcmalloc.so
```

## 默认
{{<alert title="libc-2.17.so" color="secondary">}}

{{</alert>}}
```sql
[root@k8s-node-51 ~]# ps aux|grep mysqld
mysql    12149  0.0  0.0 113420  1620 ?        S    16:42   0:00 /bin/sh /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/dbdata
mysql    13283  0.9  8.9 3913804 720376 ?      Sl   16:42   0:48 /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/my16315.conf --basedir=/data/app/mysql-8.0.26 --datadir=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/dbdata --plugin-dir=/data/app/mysql-8.0.26/lib/plugin --log-error=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/logfile/mysqld.err --open-files-limit=800000 --pid-file=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/pid/mysqld.pid --socket=/data/mysqldata/16315fff-f6c9-46cf-8934-b0d4dfb6721c/socket/mysql.sock --port=16315
root     13830  0.0  0.0 112808   964 pts/2    R+   18:08   0:00 grep --color=auto mysqld
[root@k8s-node-51 ~]# cat /proc/13283/maps |grep libc-
7f89e7049000-7f89e720d000 r-xp 00000000 08:02 918                        /usr/lib64/libc-2.17.so
7f89e720d000-7f89e740c000 ---p 001c4000 08:02 918                        /usr/lib64/libc-2.17.so
7f89e740c000-7f89e7410000 r--p 001c3000 08:02 918                        /usr/lib64/libc-2.17.so
7f89e7410000-7f89e7412000 rw-p 001c7000 08:02 918                        /usr/lib64/libc-2.17.so


```





















