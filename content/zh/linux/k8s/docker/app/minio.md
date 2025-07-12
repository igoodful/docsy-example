---
title: minio
description: minio
date: 2023-10-12
weight: 20000
---



[minio](https://dl.minio.io/server/minio/release/linux-amd64/minio)





{{<alert>}}


{{</alert>}}



```bash
mkdir -p /data/minio

docker pull minio/minio

docker run -p 9000:9000 minio/minio server /data/minio

docker run    -p 7000:7000    -p 8000:8000    --name minio    -v ~/minio/data:/data/minio    -e "MINIO_ROOT_USER=Admin123456"    -e "MINIO_ROOT_PASSWORD=Admin123456"    minio/minio server /data/minio --console-address ":8000"


```

```bash
# docker run    -p 7000:7000    -p 8000:8000    --name minio    -v ~/minio/data:/data/minio    -e "MINIO_ROOT_USER=Admin123456"    -e "MINIO_ROOT_PASSWORD=Admin123456"    minio/minio server /data/minio --console-address ":8000"

API: SYSTEM()
Time: 10:53:44 UTC 03/11/2024
DeploymentID: 20888feb-cdf2-46b9-a065-38d5c218adb5
Error: Invalid site configuration: region 'cn.igoodful' is invalid, expected simple characters such as [us-east-1, myregion...] (*fmt.wrapError)
       9: internal/logger/logger.go:260:logger.LogIf()
       8: cmd/config-current.go:535:cmd.lookupConfigs()
       7: cmd/config.go:219:cmd.initConfig()
       6: cmd/config.go:190:cmd.(*ConfigSys).Init()
       5: cmd/server-main.go:563:cmd.initConfigSubsystem()
       4: cmd/server-main.go:534:cmd.initServerConfig()
       3: cmd/server-main.go:828:cmd.serverMain.func11()
       2: cmd/server-main.go:495:cmd.bootstrapTrace()
       1: cmd/server-main.go:827:cmd.serverMain()
MinIO Object Storage Server
Copyright: 2015-2024 MinIO, Inc.
License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
Version: RELEASE.2024-03-10T02-53-48Z (go1.21.8 linux/amd64)

API: http://172.18.0.11:9000  http://127.0.0.1:9000
WebUI: http://172.18.0.11:8000 http://127.0.0.1:8000

Docs: https://min.io/docs/minio/linux/index.html
Status:         1 Online, 0 Offline.
STARTUP WARNINGS:
- Detected Linux kernel version older than 4.0.0 release, there are some known potential performance problems with this kernel version. MinIO recommends a minimum of 4.x.x linux kernel version for best performance
- The standard parity is set to 0. This can lead to data loss.

```


## MinIO界面上创建Access Keys
```json
{"url":"http://127.0.0.1:9000","accessKey":"aQH1hdCfhmLAtVivjG9D","secretKey":"VwJZQLIN23QwkjdvJAV3JTrM44GBsRHSHk3s0HwA","api":"s3v4","path":"auto"}
```




## 配置s3cmd


{{<alert>}}
- host_base，是S3服务所使用的ip地址（包括端口号），填写为MinIO的API的ip和端口
- host_bucket，为S3用户下的一个bucket(可在配置之后再创建，但该字段不能为空)，但在使用s3cmd进行文件操作前必须创建该桶
- access_key，secret_key 为上述MinIO界面上创建Access Keys的
- use_https = False
{{</alert>}}


```bash
# cat  ~/.s3cfg
[default]
access_key = aQH1hdCfhmLAtVivjG9D
secret_key = VwJZQLIN23QwkjdvJAV3JTrM44GBsRHSHk3s0HwA
host_base = 172.18.0.11:9000
host_bucket = 172.18.0.11:9000
use_https = False
human_readable_sizes = True

```









