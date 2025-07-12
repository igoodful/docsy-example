---
title: InnoDB log has wrapped
description: InnoDB log has wrapped
date: 2023-10-12
weight: 60000



---

{{< alert >}}
[InnoDB log has wrapped](https://forums.percona.com/t/xtrabackup-error-log-block-numbers-mismatch/10962/2)
{{< /alert >}}






### InnoDB log has wrapped

```sql
xtrabackup: error: log block numbers mismatch:
xtrabackup: error: expected log block no. 243298896, but got no. 268464708 from the log file.
xtrabackup: error: it looks like InnoDB log has wrapped around before xtrabackup could process all records due to either log copying being too slow, or log files being too small.
xtrabackup: Error: xtrabackup_copy_logfile() failed.
Error taking full backup
```
{{< alert color="secondary" title="解决">}}
**数据量**：3.5 TB

**当前参数**：
- innodb_log_file_size=4 GB
- innodb_log_files_in_group=3

**变更后**：
- innodb_log_file_size=15 GB
- innodb_log_files_in_group=3
{{< /alert >}}






