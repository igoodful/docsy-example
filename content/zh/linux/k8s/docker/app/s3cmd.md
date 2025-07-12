---
title: s3cmd
description: s3cmd
date: 2023-10-12
weight: 20000
---


[s3cmd](http://s3tools.org/s3cmd)




{{<alert>}}


{{</alert>}}


## 安装
```bash
 yum -y install s3cmd

```





```bash

s3cmd --configure

s3cmd --config=/root/mysql.s3cfg

s3cmd ls

s3cmd ls s3://my-bucket-name

s3cmd du -H s3://my-bucket-name
```


### 创建桶：`s3cmd mb`
> 创建 bucket，且 bucket 名称是唯一的，不能重复。相当于创建根文件夹。创建bucket时，不能用点号(".")分隔
```bash
[root@node70 /root] TTY 2 FROM 172.17.120.253
#  s3cmd mb s3://test3
Bucket 's3://test3/' created
```

### 删除空桶：`s3cmd rb`

```bash

s3cmd rb s3://bucket-name

```



### 查看桶大小：`s3cmd du -H`
```bash
# s3cmd du -H s3://tong
  21K      56 objects s3://tong/

```

### 列举所有Buckets：`s3cmd ls`
> `bucket` 相当于根文件夹
```bash
# s3cmd ls
2024-03-11 11:08  s3://test3
2024-03-11 10:30  s3://tong
2024-03-11 10:30  s3://tongtong

```

### 列举某个桶内的内容：`s3cmd ls`

```bash
s3cmd ls s3://bucket-name
```


### 上传文件：`s3cmd put`

```bash
# echo "123456" >  s3cmd.log
#  s3cmd put s3cmd.log  s3://test3
upload: 's3cmd.log' -> 's3://test3/s3cmd.log'  [1 of 1]
 7 of 7   100% in    0s   137.94 B/s  done
#
```

### 上传目录：`s3cmd put -r`

```bash

#  s3cmd put -r /tmp  s3://test3

```


### 下载文件：`s3cmd get`

```bash
#  s3cmd get s3://test3/s3cmd.log  ./
download: 's3://test3/s3cmd.log' -> './s3cmd.log'  [1 of 1]
 7 of 7   100% in    0s  1182.83 B/s  done

```


### 下载目录：`s3cmd get -r`

```bash
#  s3cmd get -r s3://test3/tmp  ./

```



### 删除文件：`s3cmd del`

```bash
# s3cmd del s3://test3/s3cmd.log
delete: 's3://test3/s3cmd.log'
```


### 删除目录：`s3cmd del -r`

```bash
# s3cmd del s3://test3/tmp

```





