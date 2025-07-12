---
title: shell分析解析后的binlog
date: 2023-10-12
weight: 20000
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.


---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


## 下载地址：https://github.com/opencontainers/runc/releases

```bash
#!/bin/bash
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc
```


```bash
grep -n "SESSION.GTID_NEXT" /tmp/binlog.txt |awk 'NR==1{tmp=$1}NR>1{print $3,$1-tmp;tmp=$1}'|sort -k2nr|head -n10





```








