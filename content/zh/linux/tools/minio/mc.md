---
title: 使用
description: mc
date: 2023-11-17
weight: 200


---

配置MinIO Access和Secret密钥，也就是登录账号和密码。 MINIO_ACCESS_KEY、MINIO_SECRET_KEY 变为 MINIO_ROOT_USER 、MINIO_ROOT_PASSWORD


1. 老版本使用MINIO_ACCESS_KEY，MINIO_SECRET_KEY，新版本使用MINIO_ROOT_USER，MINIO_ROOT_PASSWORD
2. 现在需要增加额外一个端口号用于web管理 --console-address :9090 ；API管理地址 --console-address :9000











## 用户管理

```bash
mc admin user add	添加一个新用户
mc admin user disable	禁用用户
mc admin user enable	启用用户
mc admin user remove	删除用户
mc admin user list	列出所有用户
mc admin user info	显示某一个用户信息
mc admin user policy	以 JSON 格式导出用户策略
mc admin user svcacct	管理服务帐户


```








## `mc alias ls`
```bash
2024-06-18T09:18:26 [greatdb@dts182 /data] $ mc alias ls
gcs
  URL       : https://storage.googleapis.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v2
  Path      : dns

local
  URL       : http://localhost:9000
  AccessKey :
  SecretKey :
  API       :
  Path      : auto

minio
  URL       : http://127.0.0.1:9000
  AccessKey : minio
  SecretKey : Mdf7xBGGYDAl9n5vXUlDOUHpXzlZhyBr
  API       : s3v4
  Path      : auto

play
  URL       : https://play.min.io
  AccessKey : Q3AM3UQ867SPQQA43P2F
  SecretKey : zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
  API       : S3v4
  Path      : auto

s3
  URL       : https://s3.amazonaws.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v4
  Path      : dns

```



## `mc ls minio`

```bah
2024-06-18T09:16:43 [greatdb@dts182 /data] $ mc ls minio
[0001-01-01 08:05:43 LMT]     0B dts/
2024-06-18T09:18:14 [greatdb@dts182 /data] $ mc ls --recursive minio

```



```bash
2024-06-18T12:07:10 [igoodfu@48 /db/storage/guolicheng] $ mc mb myminio/files
Bucket created successfully `myminio/files`.

2024-06-18T12:08:48 [igoodfu@48 /db/storage/guolicheng] $ mc mb /tmp/this/new/dir1
Bucket created successfully `/tmp/this/new/dir1`.
2024-06-18T12:09:19 [igoodfu@48 /db/storage/guolicheng] $

```



## 添加云存储服务


```bash
2024-06-18T13:37:53 [igoodfu@48 /db/storage/guolicheng] $ mc config host add local http://127.0.0.1:9999  minio minio1314520MINIO --api s3v4
Added `local` successfully.
2024-06-18T13:41:15 [igoodfu@48 /db/storage/guolicheng] $ mc config host list
gcs
  URL       : https://storage.googleapis.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v2
  Path      : dns

local
  URL       : http://127.0.0.1:9999
  AccessKey : minio
  SecretKey : minio1314520MINIO
  API       : s3v4
  Path      : auto

play
  URL       : https://play.min.io
  AccessKey : Q3AM3UQ867SPQQA43P2F
  SecretKey : zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
  API       : S3v4
  Path      : auto

s3
  URL       : https://s3.amazonaws.com
  AccessKey : YOUR-ACCESS-KEY-HERE
  SecretKey : YOUR-SECRET-KEY-HERE
  API       : S3v4
  Path      : dns

2024-06-18T13:41:20 [igoodfu@48 /db/storage/guolicheng] $ mc ls local
[2024-06-03 17:29:22 CST]     0B dts610/


```


## bucket管理

#### 创建bucket
> 在local minio server中创建一个data桶
```bash
2024-06-18T13:41:47 [igoodfu@48 /db/storage/guolicheng] $ mc mb local/data
Bucket created successfully `local/data`.


```

#### 查看bucket
> 指定服务名称
```bash
2024-06-18T13:44:58 [igoodfu@48 /db/storage/guolicheng] $ mc ls local
[2024-06-18 13:44:58 CST]     0B data/
[2024-06-03 17:29:22 CST]     0B dts610/

```


#### 上传一个文件到bucket
> local/data：表示指定服务的桶
```bash
2024-06-18T13:45:01 [igoodfu@48 /db/storage/guolicheng] $ mc cp /etc/passwd local/data
/etc/passwd:                                       18.73 KiB / 18.73 KiB ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.30 MiB/s 0s2024-06-18T13:49:24 [igoodfu@48 /db/storage/guolicheng] $

```

#### 上传一个目录到bucket

> local/data：表示指定服务的桶
```bash
2024-06-18T13:53:15 [igoodfu@48 /db/storage/guolicheng] $ mc cp redis_6639  local/data  --recursive
/db/storage/guolicheng/redis_6639/log/redis.log:   373.50 MiB / 373.50 MiB ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 779.61 MiB/s 0s2024-06-18T13:54:39 [igoodfu@48 /db/storage/guolicheng] $ tree redis_6639
redis_6639
├── bin
│   ├── redis-benchmark
│   ├── redis-check-aof
│   ├── redis-check-rdb
│   ├── redis-cli
│   ├── redis-sentinel
│   └── redis-server
├── conf
│   └── redis.conf
├── data
│   └── dump.rdb
├── log
│   └── redis.log
└── tmp

5 directories, 9 files
2024-06-18T13:55:40 [igoodfu@48 /db/storage/guolicheng] $



```




#### 删除bucket中的文件
> local/data：表示指定服务的桶
```bash
2024-06-18T13:55:40 [igoodfu@48 /db/storage/guolicheng] $ mc rb local/data  --force
Removed `local/data` successfully.
2024-06-18T13:57:44 [igoodfu@48 /db/storage/guolicheng] $


```
- 删除了所有data桶中的数据

```bash
2024-06-18T14:07:53 [igoodfu@48 /db/storage/guolicheng] $ mc rm  local/data/log/redis.log
Removed `local/data/log/redis.log`.
2024-06-18T14:08:21 [igoodfu@48 /db/storage/guolicheng] $ mc ls --recursive local/data
[2024-06-18 14:05:41 CST] 6.6MiB STANDARD bin/redis-benchmark
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-check-aof
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-check-rdb
[2024-06-18 14:05:41 CST] 7.3MiB STANDARD bin/redis-cli
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-sentinel
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-server
[2024-06-18 14:05:41 CST] 1.0KiB STANDARD conf/redis.conf
[2024-06-18 14:05:42 CST] 229MiB STANDARD data/dump.rdb
2024-06-18T14:08:24 [igoodfu@48 /db/storage/guolicheng] $


```








#### 查看桶中的文件
> local表示minio服务器

```bash
2024-06-18T14:05:42 [igoodfu@48 /db/storage/guolicheng] $ mc ls --recursive local/data
[2024-06-18 14:05:41 CST] 6.6MiB STANDARD bin/redis-benchmark
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-check-aof
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-check-rdb
[2024-06-18 14:05:41 CST] 7.3MiB STANDARD bin/redis-cli
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-sentinel
[2024-06-18 14:05:41 CST]  15MiB STANDARD bin/redis-server
[2024-06-18 14:05:41 CST] 1.0KiB STANDARD conf/redis.conf
[2024-06-18 14:05:42 CST] 229MiB STANDARD data/dump.rdb
[2024-06-18 14:05:41 CST]  37KiB STANDARD log/redis.log
2024-06-18T14:05:57 [igoodfu@48 /db/storage/guolicheng] $


```


#### 查看文件内容

```bash
2024-06-18T13:59:31 [igoodfu@48 /db/storage/guolicheng] $ mc cat local/dts610/validation/683030377958211648/table-results/apple.info.json
{"sourceTable":"apple.info","targetTable":"dts610_target.info","message":""}2024-06-18T14:03:40 [igoodfu@48 /db/storage/guolicheng] $

```



