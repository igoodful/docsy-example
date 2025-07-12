---
title: md5sum
description: md5sum
date: 2023-10-31
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}


## md5sum
```bash
# md5sum backup-recovery-tool-6040-Build-bc131679-kylin-aarch64.tar.gz
ca4b9bdae6717043a7f84a8112c947d9  backup-recovery-tool-6040-Build-bc131679-kylin-aarch64.tar.gz

# md5sum backup-recovery-tool-6040-Build-f2750453-kylin-x86_64.tar.gz
a6cf549fda6bf449a020ce6d99b20f5a  backup-recovery-tool-6040-Build-f2750453-kylin-x86_64.tar.gz


```



## windows上查看MD5
```bash
# certutil -hashfile my.conf
SHA1 的 my.conf 哈希:
ccfd3d30f1b4e4d254cff1d887c2250f4543e538
CertUtil: -hashfile 命令成功完成。

# certutil -hashfile my.conf md5
MD5 的 my.conf 哈希:
df561ea562c936cbe5e5443c5e657e54
CertUtil: -hashfile 命令成功完成。


```














