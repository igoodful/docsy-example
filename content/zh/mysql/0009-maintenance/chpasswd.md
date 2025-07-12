---
title: 临时修改用户密码
description: chpasswd
date: 2024-09-12
weight: 300
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;
&#9989;
$ \rightleftharpoons $

{{<note >}}

使用频率非常高

{{</note>}}

## chpasswd


```bash
#!/bin/bash
date_time=$(date +'%FT%T')
cp /etc/shadow /tmp/shadow_${date_time}
echo "username:password" |chpasswd
```


## passwd
```bash
#!/bin/bash
date_time=$(date +'%FT%T')
cp /etc/shadow /tmp/shadow_${date_time}
echo "password" | passwd --stdin username
```







