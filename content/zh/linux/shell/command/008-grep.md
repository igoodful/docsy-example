---
title: 8. grep
description: grep
date: 2023-10-31
weight: 80
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}



## 案例

### 1. 获取错误日志条数：`[ERROR]`

- `grep -c '[ERROR]' a.log` 表示会匹配E、R、O三个大写字母中任意一个字母的行数
- `grep -c '\[ERROR\]' dbscale.log` 表示匹配`[ERROR]`字符串的行数

```bash
grep -c '\[ERROR\]' dbscale.log
```

```sql
2025-01-08 14:18:27.480374 (139723771213568) [ERROR] <mysql_parser.cc:91> Error, fail to parse the stmt!
2025-01-08 14:18:27.480580 (139723771213568) [ERROR] <mysql_parser.cc:95> Error, fail to parse sql: set global "log-level" = "DEBUG"
2025-01-08 14:18:27.480592 (139723771213568) [ERROR] <mysql_parser.cc:148> No table partial parsed!
2025-01-08 14:18:27.480764 (139723771213568) [ERROR] <statement.cc:16966> Not support partial parse for set stmt when close option force-execute-partial-set:syntax error: at column 12 near '"log-level" = "DEBUG"'
2025-01-08 14:18:45.480144 (139723771213568) [ERROR] <statement.cc:11149> Current User does not have the privilege of dynamic configuration.
2025-01-08 19:41:59.947463 (139723771213568) [ERROR] <mysql_parser.cc:91> Error, fail to parse the stmt!
2025-01-08 19:42:00.008973 (139723771213568) [ERROR] <mysql_parser.cc:95> Error, fail to parse sql: select sleep(100) &
2025-01-08 19:42:00.009017 (139723771213568) [ERROR] <mysql_parser.cc:148> No table partial parsed!
2025-01-08 19:42:00.018412 (139723771213568) [ERROR] <mysql_parser.cc:91> Error, fail to parse the stmt!
2025-01-08 19:42:00.018467 (139723771213568) [ERROR] <mysql_parser.cc:95> Error, fail to parse sql: select sleep(100) &
2025-01-08 19:42:00.018484 (139723771213568) [ERROR] <mysql_parser.cc:148> No table partial parsed!
2025-01-08 19:42:00.026134 (139723771213568) [ERROR] <mysql_parser.cc:91> Error, fail to parse the stmt!
2025-01-08 19:42:00.026206 (139723771213568) [ERROR] <mysql_parser.cc:95> Error, fail to parse sql: select sleep(100) &
2025-01-08 19:42:00.026222 (139723771213568) [ERROR] <mysql_parser.cc:148> No table partial parsed!

```










