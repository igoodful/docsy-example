---
title: swap占用高，但物理内存空余
description: >
  物理内存还有剩余，但是用了swap内存，怎么处理？
date: 2023-10-17
weight: 4
---

{{< alert >}}
### 问题现象

物理内存还有剩余，但是用了swap内存，怎么处理？

当还有剩余的物理内存，但使用了swap内存，说明了可能出现以下问题：

1. 程序占用内存较大

2. 开启了swap交换分区

3. swappiness内核参数设置过大

4. 操作系统开启了numa

# 使用top查看
```sql
shell> top
top - 09:56:10 up 17 days, 10:33,  1 user,  load average: 0.23, 0.23, 0.25
Tasks: 240 total,   1 running, 239 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.3 us,  0.7 sy,  0.0 ni, 97.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 16265884 total,   232400 free, 15004100 used,  1029384 buff/cache
KiB Swap:  2097148 total,  1954292 free,   142856 used.   788268 avail Mem
```

# 使用free查看
```sql
shell> free -h
              total        used        free      shared  buff/cache   available
Mem:            15G         14G        203M         36M        956M        698M
Swap:          2.0G        139M        1.9G
```


{{< /alert >}}



{{< alert title="swappiness" color="success" >}}

1. swappiness=0 的时候表示最大限度使用物理内存，然后才是 swap空间

2. swappiness＝100 的时候表示积极的使用swap分区，并且把内存上的数据及时的搬运到swap空间里面。

3. linux的基本默认设置为60，一般默认值都是60

```bash
cat /proc/sys/vm/swappiness

sysctl vm.swappiness=0

echo "vm.swappiness=0" > /etc/sysctl.conf

sysctl -p
```

{{< /alert >}}



### swappiness值设置过大

```bash
#!/bin/bash
 cat /proc/sys/vm/swappiness


# 原因之一：swappiness值设置过大，导致积极使用swap分区。








```


### 查看操作系统是否开启numa




```bash
# 关闭numa

# 编辑 /etc/default/grub 文件，如下图所示加上：numa=off
shell> cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet numa=off"
GRUB_DISABLE_RECOVERY="true"


# 重新生成 /etc/grub2.cfg 配置文件：

shell> grub2-mkconfig -o /etc/grub2.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-957.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-957.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-ebbb68ebb9fa496d97a89e45ebf87933
Found initrd image: /boot/initramfs-0-rescue-ebbb68ebb9fa496d97a89e45ebf87933.img
done

# 重启系统
shell> reboot

# 查看是否关闭numa
shell> grep -i numa /var/log/dmesg
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-957.el7.x86_64 root=UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1 ro crashkernel=auto rhgb quiet numa=off
[    0.000000] NUMA turned off
[    0.000000] Kernel command line: BOOT_IMAGE=/vmlinuz-3.10.0-957.el7.x86_64 root=UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1 ro crashkernel=auto rhgb quiet numa=off


```


### 创建swap分区

```sql
dd if=/dev/zero of=/root/swapfile  bs=1M count=10240

mkswap /root/swapfile

swapon /root/swapfile

echo "/root/swapfile swap swap defaults 0 0" >> /etc/fstab

```



### 删除swap分区

```sql
swapoff /root/swapfile

rm -rf /root/swapfile

sed -i '/swap/d' /etc/fstab

```



## 清理buff/cache和swap空间释放

```sql
free -h
swapon  -s
sync
echo 1 > /proc/sys/vm/drop_caches
free -h
swapoff -a
free -h
swapon -a
free -h
```

- 先判断物理内存free是否大于swap使用空间。此时发现free Mem是8g，Swap是9g，所以需要先释放buff/cache

```sql
free -g
               total        used        free      shared  buff/cache   available
Mem:            125          73           8           3          43          34
Swap:             9           9           0
```

- 如果物理剩余内存不足，可以先清一下buff/cache。清理页面缓存和slab分配中的内存
```sql
echo 3 > /proc/sys/vm/drop_caches
```

- 检查free Mem
```sql
 free -g

total used free shared buff/cache available
Mem: 125 73 48 3 3 40
Swap: 9 9 0
```

4、释放完再打开自动分配
我这个操作没有生效，有人说0和>之间不要留空格就可以，我试过了，没卵用。不过也无所谓，下次服务器重启会自动置为0，不用刻意修改。

echo 0> /proc/sys/vm/drop_caches
1
5、关闭所有的交换分区
swapoff -a
1
6、再次检查free Mem
此时检查再次检查swap是否全部为0



```sql
#!/bin/bash

for i in $(cd /proc;ls|grep "^[0-9]"|awk '$1>100')
do
awk '/Swap:/{total=total+$2}END{print '"$i"',total/1024"M"}' /proc/$i/smaps >>swap_usage.txt 2>/dev/null
done

cat swap_usage.txt|sort -k2nr |head -n5|cat -n >>swap_usage_sort.txt

for id in $(cat swap_usage_sort.txt|awk '{print $1}')
do
	pid=`cat swap_usage_sort.txt|grep -w $id|awk '{print $2}'`
	pidinfo=`ps -ef|grep $pid|grep -v grep`
	swap_usage=`cat swap_usage_sort.txt|grep -w $id|awk '{print $3}'`
	echo -e "=============================================================================================="
	echo "使用率排序：$id"
	echo "进程id号：$pid"
	echo "进程信息：$pidinfo"
	echo "占用交换空间大小：$swap_usage"
	echo -e "\n"
done

[ -f swap_usage.txt ] && rm swap_usage.txt
[ -f swap_usage_sort.txt ] && rm swap_usage_sort.txt



```