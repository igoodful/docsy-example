---
title: exec
description: exec
date: 2025-04-01
weight: 30000
viz: true

---

{{< alert >}}

exec命令是Linux中的一个内置shell命令，主要用于执行可执行文件并替换当前的shell进程。执行exec命令后，原有的shell进程将被新命令替代，因此执行完该命令后不会返回到原来的shell环境。这种特性使得exec在某些情况下可以提高系统性能，因为它避免了创建新进程的开销。


{{< /alert >}}





## exec



```bash
# Ensure that fds 0, 1, and 2 are open.
if (exec 3>&0) 2>/dev/null; then :; else exec 0</dev/null; fi
if (exec 3>&1) 2>/dev/null; then :; else exec 1>/dev/null; fi
if (exec 3>&2); then :; else exec 2>/dev/null; fi

```

**上述程序的结束**：

```bash
fg 后按 ctrl + C (因为该命令是放在后台执行)
```

```bash
pkill -9 dd
```














