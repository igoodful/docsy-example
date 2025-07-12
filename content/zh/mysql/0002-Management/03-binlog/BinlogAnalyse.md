---
title: binlog分析
date: 2023-10-12
weight: 20000
description: A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
分析binlog.
{{< /alert >}}


## 统计binlog中每个GTID变更的行数


```python
#!/bin/python
# -*- coding: utf8 -*-
# DESC 分析binlog日志，生成事务耗时、更新的行数等信息。
# Python2、Python3环境均可使用

import sys
import re
import time

# binlog_name = sys.argv[1]
#binlog_name = sys.argv[1]
#if os.path.exists(binlog_name):
#    pass
#else:
#    print('文件 {} 不存在'.format(binlog_name))x
#    sys.exit(1)
# with open(binlog_name,'r') as f:
print "{0: <50} {1: <20} {2: <20} {3: <10} {4: <10}".format("trx_id", "trx_start", "trx_commit", "sql_type", "trx_rows"), "trx_sql"

with open('binlog_000018.txt','r') as f:
    while True:
        line = f.readline()
        if not line:
            break
        # 事务提交时间
        match = re.match(r"# immediate_commit_timestamp=.* \((.*)\..*", line)
        if match:
            commit_time = match.group(1)
            nums = 0
            gtid = None
            start_time = None
            trx_type = None
            trx_sql = []
            while True:
                line = f.readline()
                # 记录事务GTID
                if gtid is None:
                    match = re.match(r"(SET @@SESSION.GTID_NEXT=) '(.*?\d)'.*;", line)
                    if match:
                        gtid = match.group(2)
                        continue
                # 记录事务开始时间
                if start_time is None:
                    match = re.match(r"(SET TIMESTAMP)=(\d{10}).*;", line)
                    if match:
                        start_time = match.group(2)
                        start_time_obj = time.localtime(int(start_time))
                        start_time = time.strftime("%Y-%m-%d %H:%M:%S",start_time_obj)
                        continue
                # 记录事务类型
                match = re.search(r"### (INSERT|DELETE|UPDATE) (.*)", line,re.I)
                if match:
                    trx_type = 'DML'
                    sql = match.group(1,2)
                    if sql not in trx_sql:
                        trx_sql.append(sql)
                    nums = nums + 1
                    continue
                if trx_type is None:
                    match = re.search(r"(CREATE|DROP|ALTER|TRUNCATE) (.*)", line,re.I)
                    if match:
                        trx_type = 'DDL'
                        sql = match.group(1,2)
                        nums = "None"
                        end_time = "None"
                        #print(gtid, '"' + start_time + '"', '"' + commit_time + '"', trx_type, nums, sql)
                        print "{0: <50} {1: <20} {2: <20} {3: <10} {4: <10}".format(gtid, start_time, commit_time,trx_type, nums), sql

                        break
                # 事务结束
                match = re.match(r"(^COMMIT).*", line)
                if match:
                    #print(gtid, '"' + start_time + '"', '"' + commit_time + '"', trx_type, nums, trx_sql)
                    print "{0: <50} {1: <20} {2: <20} {3: <10} {4: <10}".format(gtid,start_time,commit_time,trx_type,nums),trx_sql
                    break

```








