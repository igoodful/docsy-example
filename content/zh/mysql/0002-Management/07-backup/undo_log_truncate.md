---
title: undo log truncate
description: undo log truncate
date: 2023-10-26
weight: 40000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
-[undo log truncate](https://opensource.actionsky.com/20211111-undolog/)
{{< /alert >}}







## 解决

#### 调大innodb_max_undo_log_size
```bash
innodb_max_undo_log_size=4G
```

#### 关闭super_read_only


```bash
super_read_only=0
```

undo log 长时间处于清理状态，该问题是由参数 super_read_only 触发的 bug。可以将super_read_only设置成0临时解决。这也解释了为什么在主节点上undo log清理的很快。

那有什么办法减少触发这个 bug 呢？我们可以将 innodb_max_undo_log_size 调大。在InnoDB 存储引擎中，undo 在完成事务回滚和 MVCC 之后，就可以 purge 掉了，purge 掉文件虽然大小不会变，但是可以被重新分配使用。

在 innodb_max_undo_log_size 设置较大的情况下，undo log 大小达到阈值前，被 purge 掉了，这样空间可以重用，很难达到阈值，所以不会触发 undo log truncate，所以也就不会触发这个 bug 。








