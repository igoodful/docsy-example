---
title: mysqld_exporter
description: mysqld_exporter
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


{{< /alert >}}



 # 安装

 > 地址：https://www.heidisql.com/download.php


以下是 `mysqld_exporter` 所有参数的详细解析，按功能分类说明：

---

### **一、基础配置**
| 参数                         | 默认值           | 说明                                                                                                                                  |
|------------------------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `--web.listen-address`       | `:9104`          | 监听的地址和端口（可重复指定多个），例如 `:9100` 或 `[::1]:9100`                                                                         |
| `--web.telemetry-path`       | `/metrics`       | Prometheus 拉取指标的路径                                                                                                             |
| `--web.config.file`          | `""`             | TLS 或认证配置文件路径（参考 [exporter-toolkit](https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md)） |
| `--web.systemd-socket`       | `false`          | 使用 systemd socket 激活（仅 Linux）                                                                                                    |
| `--config.my-cnf`            | `.my.cnf`        | MySQL 认证文件路径（格式同 `~/.my.cnf`）                                                                                                |
| `--mysqld.address`           | `localhost:3306` | MySQL 服务器地址                                                                                                                      |
| `--mysqld.username`          | 无默认值         | 连接 MySQL 的用户名                                                                                                                   |
| `--tls.insecure-skip-verify` | `false`          | 跳过 TLS 证书验证                                                                                                                     |

---

### **二、核心采集控制**
#### **1. 全局开关**
| 参数                                | 默认启用 | 说明                                               |
|-------------------------------------|----------|--------------------------------------------------|
| `--collect.global_variables`        | ✅        | 采集 `SHOW GLOBAL VARIABLES`                       |
| `--collect.global_status`           | ✅        | 采集 `SHOW GLOBAL STATUS`                          |
| `--collect.slave_status`            | ✅        | 采集主从复制状态（`SHOW SLAVE STATUS`）              |
| `--collect.info_schema.processlist` | ✅        | 采集线程状态统计（`information_schema.processlist`） |

#### **2. InnoDB 相关**
| 参数                                       | 默认启用 | 说明                                        |
|--------------------------------------------|----------|-------------------------------------------|
| `--collect.engine_innodb_status`           | ❌        | 采集 `SHOW ENGINE INNODB STATUS` 的详细状态 |
| `--collect.info_schema.innodb_metrics`     | ❌        | 采集 `information_schema.innodb_metrics`    |
| `--collect.info_schema.innodb_tablespaces` | ❌        | 采集 InnoDB 表空间信息                      |

#### **3. 性能模式（Performance Schema）**
| 参数                                     | 默认启用 | 说明                                                          |
|------------------------------------------|----------|-------------------------------------------------------------|
| `--collect.perf_schema.eventsstatements` | ✅        | 采集 SQL 语句摘要统计（`events_statements_summary_by_digest`）  |
| `--collect.perf_schema.eventswaits`      | ❌        | 采集等待事件统计（`events_waits_summary_global_by_event_name`） |
| `--collect.perf_schema.file_instances`   | ❌        | 采集文件实例统计（`file_summary_by_instance`）                  |
| `--collect.perf_schema.memory_events`    | ❌        | 采集内存事件统计（`memory_summary_global_by_event_name`）       |

---

### **三、高级采集配置**
#### **1. 过滤与限制**
| 参数                                                       | 默认值            | 说明                                    |
|------------------------------------------------------------|-------------------|---------------------------------------|
| `--collect.perf_schema.eventsstatements.limit`             | `250`             | 限制采集的 SQL 摘要数量（按响应时间排序） |
| `--collect.perf_schema.eventsstatements.timelimit`         | `86400`           | 仅采集最近 `N` 秒内出现过的 SQL 语句    |
| `--collect.perf_schema.eventsstatements.digest_text_limit` | `120`             | SQL 语句文本的最大截断长度              |
| `--collect.perf_schema.file_instances.filter`              | `.*`              | 文件实例名称的正则过滤                  |
| `--collect.perf_schema.file_instances.remove_prefix`       | `/var/lib/mysql/` | 移除文件路径前缀                        |

#### **2. 心跳监控**
| 参数                           | 默认值      | 说明                                  |
|--------------------------------|-------------|-------------------------------------|
| `--collect.heartbeat`          | ❌           | 启用心跳表监控（需配合 `pt-heartbeat`） |
| `--collect.heartbeat.database` | `heartbeat` | 心跳表所在数据库                      |
| `--collect.heartbeat.table`    | `heartbeat` | 心跳表名                              |
| `--collect.heartbeat.utc`      | ❌           | 使用 UTC 时间戳                       |

#### **3. 用户与权限**
| 参数                              | 默认启用 | 说明                     |
|-----------------------------------|----------|------------------------|
| `--collect.mysql.user`            | ❌        | 采集 `mysql.user` 表数据 |
| `--collect.mysql.user.privileges` | ❌        | 采集用户权限信息         |

---

### **四、调试与日志**
| 参数                           | 默认值   | 说明                                       |
|--------------------------------|----------|------------------------------------------|
| `--log.level`                  | `info`   | 日志级别（`debug`, `info`, `warn`, `error`） |
| `--log.format`                 | `logfmt` | 日志格式（`logfmt` 或 `json`）               |
| `--exporter.lock_wait_timeout` | `2`      | 设置连接锁超时（秒），避免元数据锁阻塞        |
| `--exporter.log_slow_filter`   | ❌        | 添加慢查询过滤（Oracle MySQL 不支持）        |
| `--timeout-offset`             | `0.25`   | 超时补偿时间（秒）                           |

---

### **五、特殊场景参数**
| 参数                                        | 说明                                                 |
|---------------------------------------------|----------------------------------------------------|
| `--collect.binlog_size`                     | 采集所有 binlog 文件的总大小                         |
| `--collect.auto_increment.columns`          | 采集自增列的最大值                                   |
| `--collect.info_schema.query_response_time` | 查询响应时间分布（需启用 `query_response_time_stats`） |
| `--collect.sys.user_summary`                | 采集 `sys.x$user_summary` 中的用户级指标             |

---

### **六、注意事项**
1. **权限要求**  
   - MySQL 用户需至少具备 `PROCESS`, `REPLICATION CLIENT`, `SELECT` 权限。
   - 若采集 `performance_schema`，需确保 MySQL 已启用该模块。

2. **性能影响**  
   - 启用过多采集项（如 `perf_schema`）可能增加 MySQL 负载。
   - 高频采集时建议调整 `timeout-offset`。

3. **配置文件示例**  
   ```ini
   # .my.cnf 示例
   [client]
   user = exporter
   password = your_password
   host = 127.0.0.1
   port = 3306
   ```

---

### **七、常用命令示例**
```bash
# 基础启动（使用默认配置）
./mysqld_exporter --config.my-cnf=/path/to/.my.cnf

# 启用 InnoDB 状态和慢查询监控
./mysqld_exporter \
  --collect.engine_innodb_status \
  --collect.perf_schema.eventsstatements

# 监听多个端口并启用 TLS
./mysqld_exporter \
  --web.listen-address=":9104" \
  --web.listen-address="[::1]:9105" \
  --web.config.file=web-config.yml
```

通过合理配置这些参数，可以灵活监控 MySQL 的关键指标，同时平衡性能开销。














