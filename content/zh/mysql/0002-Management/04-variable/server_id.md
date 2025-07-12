---
title: server_id
description: 备份恢复丢失数据
date: 2023-10-26
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
版本：mysql-8.0.32

- [replicate-same-server-id](https://opensource.actionsky.com/20200316-mysql/)
- [server_id](https://opensource.actionsky.com/20200316-mysql/)

{{< /alert >}}





## 备份恢复时，






由此可见，在这种情况下，更换 server_id 是最简单的解决办法，所以我们在恢复实例的时候，尽可能的去修改 server_id，保证 server_id 不与之前的重复且与集群中的其他实例不相同。

有不少人并没有碰到类似的问题，是因为他们在恢复实例的时候，通常是直接在线备份线上的实例，用最新的备份集来导入数据，在这种情况下，也可以避免以上问题的发生。





