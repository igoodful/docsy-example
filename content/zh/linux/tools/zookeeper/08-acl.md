---
title: 8. ACL
description: ACL
date: 2025-06-19
weight: 800
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

zookeeper 的 acl 通过 `[scheme:id:permissions]` 来构成权限列表。

1. scheme：代表采用的某种权限机制，包括 world、auth、digest、ip、super 几种。
2. id：代表允许访问的用户。
3. permissions：权限组合字符串，由 cdrwa 组成，其中每个字母代表支持不同权限， 创建权限 create(c)、删除权限 delete(d)、读权限 read(r)、写权限 write(w)、管理权限admin(a)。

{{< /alert >}}


## 权限控制方案

- `echo -n username:password | openssl dgst -binary -sha1 | openssl base64`

```viz-dot
digraph "权限控制方案" {
rankdir = LR;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"权限控制方案" -> "super";
"权限控制方案" -> "digest";
"权限控制方案" -> "auth";
"权限控制方案" -> "world";
"权限控制方案" -> "ip";
"权限控制方案" -> "x509";
"super" -> "-Dzookeeper.DigestAuthenticationProvider.superDigest=admin:0uek/hZ/V9fgiM35b0Z2226acMQ=";
"digest" -> "setAcl /dbscale digest:admin:0uek/hZ/V9fgiM35b0Z2226acMQ=:cdrwa";
"auth" -> "setAcl /dbscale auth:username::crdwa";
"world" -> "setAcl /dbscale world:anyone:cdrwa";
"ip" -> "setAcl /dbscale ip:10.10.10.10:cdrwa";
"ip" -> "setAcl /dbscale ip:10.10.10.1/24:cdrwa";

}
```

- world 只有一个对象为anyone
- ip 表示有相同 IP 地址的任何用户，通过 IP 地址粒度来进行权限控制
-


```bash
[mysql@node66 /home/mysql] $ echo -n admin:123456 | openssl dgst -binary -sha1 | openssl base64
0uek/hZ/V9fgiM35b0Z2226acMQ=


```



## 权限类型


```viz-dot
digraph "权限类型" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"权限类型" -> "c";
"权限类型" -> "d";
"权限类型" -> "r";
"权限类型" -> "w";
"权限类型" -> "a";

}
```




## 权限命令


```viz-dot
digraph "权限命令" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"权限命令" -> "getAcl";
"权限命令" -> "setAcl";
"权限命令" -> "addauth";

}
```


