---
title: innodb_doublewrite_pages
description: innodb_doublewrite_pages
date: 2023-10-25
weight: 20000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


{{< /alert >}}


## 参数



MySQL 8.0中存在的性能回归问题主要体现在表重建类操作的执行效率显著下降

具体表现为：核心问题在MySQL 8.0（尤其是8.0.20及之后版本）中，执行ALTER TABLE ... FORCE等表重建操作时，其性能相比5.7版本会出现3倍以上的显著下降。

该问题在2024年7月被确认由参数innodb_doublewrite_pages的默认值设置不当引发。根本原因参数变更引入缺陷

MySQL 8.0.20引入了innodb_doublewrite_pages参数（默认值4），该参数控制InnoDB双写机制中每次批量写入的页数。

较低的值导致：I/O操作更频繁双写缓冲区刷新效率降低磁盘同步成本显著增加历史行为差异

在MySQL 5.7中，虽然该参数未正式存在，但其底层实现等效于默认值128，因此表重建操作的I/O效率更高。

文档与修复延迟尽管MySQL 8.4.0已将该参数默认值修正为128，但8.0版本因LTS（长期支持）的"稳定性"承诺未调整默认值，导致用户需手动配置。

受影响场景该问题在以下操作中表现尤为突出：使用INPLACE算法重建表（如ALTER TABLE ... FORCE）涉及大规模数据页写入的DDL操作高并发写入场景下的双写缓冲区竞争

官方建议临时解决方案：将innodb_doublewrite_pages设置为128（需重启实例）

长期方案：升级至MySQL 8.4+版本

监控建议：关注Innodb_dblwr_pages_written和Innodb_dblwr_writes指标的变化（注：该问题已在MySQL 8.4版本中通过调整默认值修复，但截至2025年4月，MySQL 8.0仍保持原有默认值）











