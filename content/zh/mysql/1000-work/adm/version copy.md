---
title: 问题处理
description: version
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [ADM运维文档](https://bbkv6krkep.feishu.cn/wiki/VUlvwuaAtip7UKkA8W4cCr0enxf)

{{< /alert >}}






### 保证防火墙关闭
>   如果不能关闭防火墙，需要开启端口策略，放行安装脚本中指定的端口
```bash
systemctl status firewalld

systemctl stop firewalld
```


### 保证主机系统时间差很小

> 若两个主机的系统时间差的比较多，则
```bash
systemctl status adm-dbinit

journalctl -u adm-dbinit.service  --since "1 hour ago"

```


### 保证主机上的`dbinit`服务正常


```bash
ps -ef |grep adm-dbinit

# 服务端口为19999
```


### 保证`adm`所在容器到纳管主机网络联通
> `adm`和`dbinit`服务端口(`19999`)是否联通

```bash
curl ip:port
```
如果请求没有任何响应，说明网络不通，需要检查是哪个环节有问题（可以参考第一个问题：ADM无法访问）
- 可能是docker容器到主机网络不通
- 可能是主机的防火墙没有放行19999端口











