---
title: 1. 文件管理
description: file
date: 2023-10-24
weight: 10
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}

## 一、概览
#### 1. 目录
```viz-dot
digraph "目录" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "目录" -> "mkdir";
        "目录" -> "rmdir";
        "目录" -> "pwd";
        "目录" -> "cd";
}
```

#### 2. 文件

```viz-dot
digraph "文件" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "文件" -> "touch";
        "文件" -> "echo";
        "文件" -> "split";
        "文件" -> "md5sum";
        "文件" -> "vim";
        "文件" -> "cat";
        "文件" -> "tac";
        "文件" -> "more";
        "文件" -> "less";
        "文件" -> "head";
        "文件" -> "tail";


}
```

#### 3. 目录和文件
```viz-dot
digraph "目录和文件" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "目录和文件" -> "clear";
        "目录和文件" -> "ls";
        "目录和文件" -> "ln";
        "目录和文件" -> "cp";
        "目录和文件" -> "scp";
        "目录和文件" -> "mv";
        "目录和文件" -> "rm";
        "目录和文件" -> "find";
        "目录和文件" -> "chown";
        "目录和文件" -> "chmod";
        "目录和文件" -> "du";
        "目录和文件" -> "stat";
        "目录和文件" -> "chattr";
}
```

## 二、目录

#### 1. mkdir



#### 2. rmdir

#### 3. pwd



#### 4. cd



## 三、文件


#### touch

#### echo

#### split

{{< alert title="split参数详解" color="secondary">}}

1. 确定文件切割标准：
   - 按行切割：`-l, --lines=NUMBER`
     - `-10` 或者 `-l 10`
   - 按大小切割：`-b, --bytes=SIZE` （单位为 byte。也可以指定K、M、G等单位，一个字节=8位二进制数(1byte=8bit)）
     - `split -b 10k file`
     - `split -b 10M file`


2. 确定切割文件的文件名
   - 指定文件后缀名称：`--additional-suffix=SUFFIX`
     - `--additional-suffix=.log`
   - 指定文件前缀名称：`split --verbose -b 300k  -d -a 3  --additional-suffix=.suffix messages prefix`
   - 指定文件后缀长度：`-a, --suffix-length=N` 指定后缀长度，默认后缀长度是2，即按 aa，ab，ac的顺序排序
     - `-a 5`，输出文件结果：x_aaaaa、x_aaaab。
   - 指定文件后缀为字母还是数字：`-d, --numeric-suffixes`：使用数字作为后缀名，默认是字母


- `-n, --number=CHUNKS`：分割为指定数量的输出文件。CHUNKS可以是以下指定形式：
  - N：根据大小将输入数据分割为N个文件。
  - K/N：输出第K个文件（共N个，K从1开始）到标准输出。
  - l/N：分割为N个文件，不拆分行/记录（l为字母L的小写）。
  - l/K/N：输出第K个文件到标准输出，不拆分行/记录。
  - r/N：类似于l/N，但使用轮询分布。
  - r/K/N：类似于 r/N，但是只将第K个文件输出到标准输出。
- `--verbose` 获取拆分创建文件的过程
- `--help` : 在线帮助
- `--version` : 显示版本信息


{{< /alert >}}


默认情况：默认情况下是按照行数拆分，一个文件1000行，拆分后的文件名以x_为前缀，后面拼接aa、ab、ac...dc等



###### 1. 按照大小切割


```bash

# ll
total 1948
-rw------- 1 root root 1992433 Mar 14 19:37 messages

# split --verbose -b 300k messages -d -a 3 bigflie --additional-suffix=.log
creating file 'bigflie000.log'
creating file 'bigflie001.log'
creating file 'bigflie002.log'
creating file 'bigflie003.log'
creating file 'bigflie004.log'
creating file 'bigflie005.log'
creating file 'bigflie006.log'

# ll
total 3896
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie000.log
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie001.log
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie002.log
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie003.log
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie004.log
-rw-r--r-- 1 root root  307200 Mar 14 20:36 bigflie005.log
-rw-r--r-- 1 root root  149233 Mar 14 20:36 bigflie006.log
-rw------- 1 root root 1992433 Mar 14 19:37 messages

```

###### 2. 按照行数切割


`split -l 1000 --numeric-suffixes=1 messages messages_`
```bash
# ll
total 1948
-rw------- 1 root root 1992433 Mar 14 19:37 messages

# split -l 3000 --numeric-suffixes=1 messages messages_

# ll
total 3920
-rw------- 1 root root 1992433 Mar 14 19:37 messages
-rw-r--r-- 1 root root  270407 Mar 14 19:40 messages_01
-rw-r--r-- 1 root root  270486 Mar 14 19:40 messages_02
-rw-r--r-- 1 root root  270531 Mar 14 19:40 messages_03
-rw-r--r-- 1 root root  270490 Mar 14 19:40 messages_04
-rw-r--r-- 1 root root  263319 Mar 14 19:40 messages_05
-rw-r--r-- 1 root root  275855 Mar 14 19:40 messages_06
-rw-r--r-- 1 root root  268473 Mar 14 19:40 messages_07
-rw-r--r-- 1 root root  102872 Mar 14 19:40 messages_08

```

`split -l 1000 messages -d -a 3 messages_`
```bash
# ll
total 1948
-rw------- 1 root root 1992433 Mar 14 19:37 messages

# split -l 1000 messages -d -a 3 messages_

# ll
total 3948
-rw------- 1 root root 1992433 Mar 14 19:37 messages
-rw-r--r-- 1 root root   90184 Mar 14 19:47 messages_000
-rw-r--r-- 1 root root   90076 Mar 14 19:47 messages_001
-rw-r--r-- 1 root root   90147 Mar 14 19:47 messages_002
-rw-r--r-- 1 root root   90131 Mar 14 19:47 messages_003
-rw-r--r-- 1 root root   90182 Mar 14 19:47 messages_004
-rw-r--r-- 1 root root   90173 Mar 14 19:47 messages_005
-rw-r--r-- 1 root root   90177 Mar 14 19:47 messages_006
-rw-r--r-- 1 root root   90275 Mar 14 19:47 messages_007
-rw-r--r-- 1 root root   90079 Mar 14 19:47 messages_008
-rw-r--r-- 1 root root   90091 Mar 14 19:47 messages_009
-rw-r--r-- 1 root root   90100 Mar 14 19:47 messages_010
-rw-r--r-- 1 root root   90299 Mar 14 19:47 messages_011
-rw-r--r-- 1 root root   90190 Mar 14 19:47 messages_012
-rw-r--r-- 1 root root   89885 Mar 14 19:47 messages_013
-rw-r--r-- 1 root root   83244 Mar 14 19:47 messages_014
-rw-r--r-- 1 root root   96632 Mar 14 19:47 messages_015
-rw-r--r-- 1 root root   89593 Mar 14 19:47 messages_016
-rw-r--r-- 1 root root   89630 Mar 14 19:47 messages_017
-rw-r--r-- 1 root root   89200 Mar 14 19:47 messages_018
-rw-r--r-- 1 root root   90133 Mar 14 19:47 messages_019
-rw-r--r-- 1 root root   89140 Mar 14 19:47 messages_020
-rw-r--r-- 1 root root   90143 Mar 14 19:47 messages_021
-rw-r--r-- 1 root root   12729 Mar 14 19:47 messages_022
```
**说明**：
- -l 按行分割
- -d 添加数字后缀，而不是默认的字母后缀
- -a 3 表示用3位数来顺序命名后缀长度
- messages_ 分割后文件的前缀





#### md5sum
> `md5sum`

```bash
md5sum filename
```

```bash
# windows
certutil -hashfile filename MD5
```


#### cat

#### tac

#### more

#### less

#### head

#### tail

#### vim



## 四、文件和目录


#### rm

```bash
find ./ -inum 51840270 -exec rm -rf {} \;
```

#### stat

- `stat slow.log`
```bash
  File: ‘slow.log’
  Size: 106946671 	Blocks: 208896     IO Block: 4096   regular file
Device: 802h/2050d	Inode: 1445888     Links: 1
Access: (0640/-rw-r-----)  Uid: ( 1001/   mysql)   Gid: ( 1001/   mysql)
Access: 2024-05-18 16:07:05.564278697 +0800
Modify: 2024-05-18 17:07:46.759246911 +0800
Change: 2024-05-18 17:07:46.759246911 +0800
 Birth: -

```


#### find


###### 文件名称
{{< alert title="-name" color="secondary">}}
**-name -iname**：
- d: 目录
- c: 字型装置档案
- b: 区块装置档案
- p: 具名贮列
- f: 普通文件
- l: 符号连结
- s: socket

{{< /alert >}}



###### 文件类型
{{< alert title="-type" color="secondary">}}

- d: 目录
- c: 字型装置档案
- b: 区块装置档案
- p: 管道文件
- f: 普通文件
- l: 符号连结
- s: 套接字文件

{{< /alert >}}



######  文件大小
{{< alert title="find参数详解" color="secondary">}}
- 50k:   搜索49k~50k大小的文件   N-1~N
- +50k:  搜索大于50k的文件       N~······
- -50k:  搜索小于49k的文件       0~N-1

- -size  2M  查找等于2M的文件
- -size -2M  查找小于2M的文件
- -size +2M  查找大于2M的文件
  - G
  - M
  - k
  - c
  - b

{{< /alert >}}


######  文件权限
{{< alert title="-perm" color="secondary">}}

-perm [/|-] MODE

MODE：精确匹配三类对象(u,g,o)的权限
  /MODE：三类对象只要有一类对象中的三个权限位匹配一位即可，或关系(/444,三个只要有一 个“有”读权限(注意不是“是”读权限,))
  -MODE：三类对象分别有对应权限，是否还有其他权限不在意，与关系(-444,三个都要“有”读权限，可以匹配到4,6,7；是否还有写或者执行权限不关心)
  Notes：/或者-后面需要匹配的权限出现0(例如404)，,则表明不关心该对象权限(例如440,所属组有无权限都不关心)。如果单独的权限表示精确匹配，则表明该对象无任何权限
{{< /alert >}}


######  文件时间
{{< alert title="-mtime" color="secondary">}}

- -atime [+|-]#
  - +: 表示（#+1）天之外被访问过；
  - -: 表示#天之内被访问过；
  - 无符号：表示短于（#+1）> x >=#天的时间段被访问过；
- -mtime:修改时间
- -ctime:创建时间

 以分钟为单位(min)：
- -amin [+|-]#:访问时间
- -mmin:修改时间
- -cmin:创建时间


{{< /alert >}}


```bash
 find . -type f -newermt '2022-08-17 00:00:00'  # 找到比2022-08-17 0时0分0秒修改的文件

```











######  用户和属组
{{< alert title="find参数详解" color="secondary">}}

- -user username      查找属主为指定用户(UID)的文件
- -group groupname    查找属组为指定组(GID)的文件
- -uid UseerID        查找属主为指定的UID号的文件
- -gid GroupID        查找属组为指定的GID号的文件
- -nouser             查找没有属主的文件
- -nogroup            查找没有属组的文件


{{< /alert >}}

###### exec

```bash
find ./mysql-server-8032-format/ -name "*.cc" -exec clang-format -i {} \;
```




#### chattr



###### 递归添加文件不可删除属性
```bash
chattr +i -RV /data
```


- 准备：
  - `mkdir /root/nb`
  - `cp /etc/passwd /etc/hosts /etc/hostname /root/nb/`
  - `ls -l /root/nb`
```shell
# 输出：
total 12
-rw-r--r-- 1 root root    4 May 18 18:58 hostname
-rw-r--r-- 1 root root  261 May 18 18:58 hosts
-rw-r--r-- 1 root root 1177 May 18 18:58 passwd

```



- 执行：`chattr +i -RV /root/nb`
```bash
# 输出：
chattr 1.45.6 (20-Mar-2020)
Flags of /root/nb set as ----i---------------
Flags of /root/nb/passwd set as ----i---------------
Flags of /root/nb/hosts set as ----i---------------
Flags of /root/nb/hostname set as ----i---------------

```


- 删除文件：`rm -rf /root/nb`
```bash
# 输出：
rm: cannot remove '/root/nb/passwd': Operation not permitted
rm: cannot remove '/root/nb/hosts': Operation not permitted
rm: cannot remove '/root/nb/hostname': Operation not permitted

```


- 去掉chattr属性：`chattr -i -RV /root/nb`
```bash
# 输出：
chattr 1.45.6 (20-Mar-2020)
Flags of /root/nb set as --------------------
Flags of /root/nb/passwd set as --------------------
Flags of /root/nb/hosts set as --------------------
Flags of /root/nb/hostname set as --------------------

```







