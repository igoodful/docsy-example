---
title: mysqld_exporter指标详解
description: mysqld_exporter指标详解
date: 2023-11-05
weight: 31000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}


{{</alert>}}



## 指标详解




## show binary logs

二进制日志个数、大小、序号

{{<alert color="danger" title="注意" >}}

- `show binary logs;` 执行结果的所有二进制日志个数作为指标`mysql_binlog_files`的值
- `show binary logs;` 执行结果的最后一个binlog文件名称中的序号作为指标`mysql_binlog_file_number`的值
- `show binary logs;` 执行结果的所有二进制日志大小求和作为指标`mysql_binlog_file_number`的值`mysql_binlog_size_bytes`

{{</alert>}}


```sql
[root@127.0.0.1:8032 21:11:28((none))]$ show binary logs;
+------------+------------+-----------+
| Log_name   | File_size  | Encrypted |
+------------+------------+-----------+
| bin.000021 | 1074021864 | No        |
| bin.000022 | 1074149105 | No        |
| bin.000023 | 1074890846 | No        |
| bin.000024 | 1074353907 | No        |
| bin.000025 | 1086789357 | No        |
| bin.000026 | 1074389658 | No        |
| bin.000027 | 1078013859 | No        |
| bin.000028 | 1104417848 | No        |
| bin.000029 | 1083977122 | No        |
| bin.000030 | 1091023656 | No        |
| bin.000031 | 1077170513 | No        |
| bin.000032 | 1077324786 | No        |
| bin.000033 | 1074141680 | No        |
| bin.000034 | 1076896406 | No        |
| bin.000035 | 1080481492 | No        |
| bin.000036 | 1073969142 | No        |
| bin.000037 | 1075111062 | No        |
| bin.000038 | 1076376567 | No        |
| bin.000039 | 1075581363 | No        |
| bin.000040 | 1076698366 | No        |
| bin.000041 | 1084074373 | No        |
| bin.000042 | 1080541307 | No        |
| bin.000043 |  761502999 | No        |
| bin.000044 | 1162608626 | No        |
| bin.000045 | 1075814384 | No        |
| bin.000046 | 1074752344 | No        |
| bin.000047 | 1074747476 | No        |
| bin.000048 | 1073742280 | No        |
| bin.000049 |  754509769 | No        |
| bin.000050 |        230 | No        |
| bin.000051 |        193 | No        |
+------------+------------+-----------+
31 rows in set (0.00 sec)

[root@127.0.0.1:8032 21:15:56((none))]$ flush logs;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 21:16:03((none))]$ flush logs;
Query OK, 0 rows affected (0.07 sec)

[root@127.0.0.1:8032 21:16:04((none))]$ show binary logs;
+------------+------------+-----------+
| Log_name   | File_size  | Encrypted |
+------------+------------+-----------+
| bin.000021 | 1074021864 | No        |
| bin.000022 | 1074149105 | No        |
| bin.000023 | 1074890846 | No        |
| bin.000024 | 1074353907 | No        |
| bin.000025 | 1086789357 | No        |
| bin.000026 | 1074389658 | No        |
| bin.000027 | 1078013859 | No        |
| bin.000028 | 1104417848 | No        |
| bin.000029 | 1083977122 | No        |
| bin.000030 | 1091023656 | No        |
| bin.000031 | 1077170513 | No        |
| bin.000032 | 1077324786 | No        |
| bin.000033 | 1074141680 | No        |
| bin.000034 | 1076896406 | No        |
| bin.000035 | 1080481492 | No        |
| bin.000036 | 1073969142 | No        |
| bin.000037 | 1075111062 | No        |
| bin.000038 | 1076376567 | No        |
| bin.000039 | 1075581363 | No        |
| bin.000040 | 1076698366 | No        |
| bin.000041 | 1084074373 | No        |
| bin.000042 | 1080541307 | No        |
| bin.000043 |  761502999 | No        |
| bin.000044 | 1162608626 | No        |
| bin.000045 | 1075814384 | No        |
| bin.000046 | 1074752344 | No        |
| bin.000047 | 1074747476 | No        |
| bin.000048 | 1073742280 | No        |
| bin.000049 |  754509769 | No        |
| bin.000050 |        230 | No        |
| bin.000051 |        230 | No        |
| bin.000052 |        230 | No        |
| bin.000053 |        193 | No        |
+------------+------------+-----------+
33 rows in set (0.00 sec)

[root@127.0.0.1:8032 21:16:06((none))]$



```


- `/root/mysqld_exporter-0.15.1/collector/binlog.go`
```go
// Copyright 2018 The Prometheus Authors
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
// http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

// Scrape `SHOW BINARY LOGS`

package collector

import (
	"context"
	"database/sql"
	"fmt"
	"strconv"
	"strings"

	"github.com/go-kit/log"
	"github.com/prometheus/client_golang/prometheus"
)

const (
	// Subsystem.
	binlog = "binlog"
	// Queries.
	logbinQuery = `SELECT @@log_bin`
	binlogQuery = `SHOW BINARY LOGS`
)

// Metric descriptors.
var (
	binlogSizeDesc = prometheus.NewDesc(
		prometheus.BuildFQName(namespace, binlog, "size_bytes"),
		"Combined size of all registered binlog files.",
		[]string{}, nil,
	)
	binlogFilesDesc = prometheus.NewDesc(
		prometheus.BuildFQName(namespace, binlog, "files"),
		"Number of registered binlog files.",
		[]string{}, nil,
	)
	binlogFileNumberDesc = prometheus.NewDesc(
		prometheus.BuildFQName(namespace, binlog, "file_number"),
		"The last binlog file number.",
		[]string{}, nil,
	)
)

// ScrapeBinlogSize collects from `SHOW BINARY LOGS`.
type ScrapeBinlogSize struct{}

// Name of the Scraper. Should be unique.
func (ScrapeBinlogSize) Name() string {
	return "binlog_size"
}

// Help describes the role of the Scraper.
func (ScrapeBinlogSize) Help() string {
	return "Collect the current size of all registered binlog files"
}

// Version of MySQL from which scraper is available.
func (ScrapeBinlogSize) Version() float64 {
	return 5.1
}

// Scrape collects data from database connection and sends it over channel as prometheus metric.
func (ScrapeBinlogSize) Scrape(ctx context.Context, db *sql.DB, ch chan<- prometheus.Metric, logger log.Logger) error {
	var logBin uint8
	err := db.QueryRowContext(ctx, logbinQuery).Scan(&logBin)
	if err != nil {
		return err
	}
	// If log_bin is OFF, do not run SHOW BINARY LOGS which explicitly produces MySQL error
	if logBin == 0 {
		return nil
	}

	masterLogRows, err := db.QueryContext(ctx, binlogQuery)
	if err != nil {
		return err
	}
	defer masterLogRows.Close()

	var (
		size      uint64
		count     uint64
		filename  string
		filesize  uint64
		encrypted string
	)
	size = 0
	count = 0

	columns, err := masterLogRows.Columns()
	if err != nil {
		return err
	}
	columnCount := len(columns)

	for masterLogRows.Next() {
		switch columnCount {
		case 2:
			if err := masterLogRows.Scan(&filename, &filesize); err != nil {
				return nil
			}
		case 3:
			if err := masterLogRows.Scan(&filename, &filesize, &encrypted); err != nil {
				return nil
			}
		default:
			return fmt.Errorf("invalid number of columns: %q", columnCount)
		}

		size += filesize
		count++
	}

	ch <- prometheus.MustNewConstMetric(
		binlogSizeDesc, prometheus.GaugeValue, float64(size),
	)
	ch <- prometheus.MustNewConstMetric(
		binlogFilesDesc, prometheus.GaugeValue, float64(count),
	)
	// The last row contains the last binlog file number.
	value, _ := strconv.ParseFloat(strings.Split(filename, ".")[1], 64)
	ch <- prometheus.MustNewConstMetric(
		binlogFileNumberDesc, prometheus.GaugeValue, value,
	)

	return nil
}

// check interface
var _ Scraper = ScrapeBinlogSize{}


```


## show engine innodb status;




{{<alert color="danger" title="注意" >}}

- `show engine innodb status;` 执行结果的所有二进制日志个数作为指标`mysql_engine_innodb_queries_in_queue`的值
- `show engine innodb status;` 执行结果的所有二进制日志个数作为指标`mysql_engine_innodb_queries_inside_innodb`的值
- `show engine innodb status;` 执行结果的所有二进制日志个数作为指标`mysql_engine_innodb_read_views_open_inside_innodb`的值

{{</alert>}}



##

- `mysql_up`
- `mysql_exporter_collector_success`
- `mysql_exporter_collector_duration_seconds`

详解

- `mysql_exporter_collector_success`
```json
mysql_exporter_collector_success{collector="collect.binlog_size", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.binlog_size", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.engine_innodb_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.engine_innodb_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.global_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.global_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.global_variables", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.global_variables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_cmp", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_cmp", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_cmpmem", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_cmpmem", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_metrics", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.innodb_metrics", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.processlist", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.processlist", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.query_response_time", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.info_schema.query_response_time", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.perf_schema.file_events", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.perf_schema.file_events", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.perf_schema.replication_group_member_stats", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.perf_schema.replication_group_member_stats", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.slave_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_success{collector="collect.slave_status", instance="172.17.130.48:9105", job="mysql_exporter"}

```

- `mysql_exporter_collector_duration_seconds`
```json

mysql_exporter_collector_duration_seconds{collector="collect.binlog_size", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.binlog_size", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.engine_innodb_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.engine_innodb_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.global_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.global_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.global_variables", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.global_variables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_cmp", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_cmp", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_cmpmem", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_cmpmem", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_metrics", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.innodb_metrics", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.processlist", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.processlist", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.query_response_time", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.info_schema.query_response_time", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.perf_schema.file_events", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.perf_schema.file_events", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.perf_schema.replication_group_member_stats", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.perf_schema.replication_group_member_stats", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.slave_status", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="collect.slave_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="connection", instance="172.17.130.48:9104", job="mysql_exporter"}
mysql_exporter_collector_duration_seconds{collector="connection", instance="172.17.130.48:9105", job="mysql_exporter"}

```






## show global status;


{{<alert color="danger" title="注意" >}}

- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_commands_total`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_handlers_total`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_connection_errors_total`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_buffer_pool_pages`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_buffer_pool_dirty_pages`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_buffer_pool_page_changes_total`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_innodb_row_ops_total`的值
- `show global status;` 执行结果的所有二进制日志个数作为指标`mysql_global_status_performance_schema_lost_total`的值


{{</alert>}}



#### `mysql_global_status_commands_total`

Total number of executed MySQL commands


- SQL
```sql
[root@127.0.0.1:8032 18:29:32((none))]$ show global status like '%com%';
+-------------------------------------+----------+
| Variable_name                       | Value    |
+-------------------------------------+----------+
| Com_admin_commands                  | 30092    |
| Com_assign_to_keycache              | 0        |
| Com_alter_db                        | 0        |
| Com_alter_event                     | 0        |
| Com_alter_function                  | 0        |
| Com_alter_instance                  | 0        |
| Com_alter_procedure                 | 0        |
| Com_alter_resource_group            | 0        |
| Com_alter_server                    | 0        |
| Com_alter_table                     | 3        |
| Com_alter_tablespace                | 0        |
| Com_alter_user                      | 0        |
| Com_alter_user_default_role         | 0        |
| Com_analyze                         | 0        |
| Com_begin                           | 659156   |
| Com_binlog                          | 0        |
| Com_call_procedure                  | 0        |
| Com_change_db                       | 18       |
| Com_change_master                   | 0        |
| Com_change_repl_filter              | 0        |
| Com_change_replication_source       | 0        |
| Com_check                           | 0        |
| Com_checksum                        | 0        |
| Com_clone                           | 0        |
| Com_commit                          | 659135   |
| Com_create_db                       | 3        |
| Com_create_event                    | 0        |
| Com_create_function                 | 0        |
| Com_create_index                    | 10       |
| Com_create_procedure                | 0        |
| Com_create_role                     | 0        |
| Com_create_server                   | 0        |
| Com_create_table                    | 61       |
| Com_create_resource_group           | 0        |
| Com_create_trigger                  | 0        |
| Com_create_udf                      | 0        |
| Com_create_user                     | 1        |
| Com_create_view                     | 0        |
| Com_create_spatial_reference_system | 0        |
| Com_dealloc_sql                     | 0        |
| Com_delete                          | 659142   |
| Com_delete_multi                    | 0        |
| Com_do                              | 0        |
| Com_drop_db                         | 0        |
| Com_drop_event                      | 0        |
| Com_drop_function                   | 0        |
| Com_drop_index                      | 0        |
| Com_drop_procedure                  | 0        |
| Com_drop_resource_group             | 0        |
| Com_drop_role                       | 0        |
| Com_drop_server                     | 0        |
| Com_drop_spatial_reference_system   | 0        |
| Com_drop_table                      | 3        |
| Com_drop_trigger                    | 0        |
| Com_drop_user                       | 0        |
| Com_drop_view                       | 0        |
| Com_empty_query                     | 0        |
| Com_execute_sql                     | 0        |
| Com_explain_other                   | 0        |
| Com_flush                           | 7        |
| Com_get_diagnostics                 | 0        |
| Com_grant                           | 1        |
| Com_grant_roles                     | 0        |
| Com_ha_close                        | 0        |
| Com_ha_open                         | 0        |
| Com_ha_read                         | 0        |
| Com_help                            | 0        |
| Com_import                          | 0        |
| Com_insert                          | 5543987  |
| Com_insert_select                   | 3        |
| Com_install_component               | 0        |
| Com_install_plugin                  | 0        |
| Com_kill                            | 0        |
| Com_load                            | 0        |
| Com_lock_instance                   | 0        |
| Com_lock_tables                     | 0        |
| Com_optimize                        | 0        |
| Com_preload_keys                    | 0        |
| Com_prepare_sql                     | 0        |
| Com_purge                           | 0        |
| Com_purge_before_date               | 0        |
| Com_release_savepoint               | 0        |
| Com_rename_table                    | 0        |
| Com_rename_user                     | 0        |
| Com_repair                          | 0        |
| Com_replace                         | 0        |
| Com_replace_select                  | 0        |
| Com_reset                           | 0        |
| Com_resignal                        | 0        |
| Com_restart                         | 0        |
| Com_revoke                          | 0        |
| Com_revoke_all                      | 0        |
| Com_revoke_roles                    | 0        |
| Com_rollback                        | 6        |
| Com_rollback_to_savepoint           | 0        |
| Com_savepoint                       | 0        |
| Com_select                          | 4239691  |
| Com_set_option                      | 30121    |
| Com_set_password                    | 0        |
| Com_set_resource_group              | 0        |
| Com_set_role                        | 0        |
| Com_signal                          | 0        |
| Com_show_binlog_events              | 0        |
| Com_show_binlogs                    | 30097    |
| Com_show_charsets                   | 0        |
| Com_show_collations                 | 0        |
| Com_show_create_db                  | 0        |
| Com_show_create_event               | 0        |
| Com_show_create_func                | 0        |
| Com_show_create_proc                | 0        |
| Com_show_create_table               | 13       |
| Com_show_create_trigger             | 0        |
| Com_show_databases                  | 3        |
| Com_show_engine_logs                | 0        |
| Com_show_engine_mutex               | 0        |
| Com_show_engine_status              | 30094    |
| Com_show_events                     | 0        |
| Com_show_errors                     | 0        |
| Com_show_fields                     | 5        |
| Com_show_function_code              | 0        |
| Com_show_function_status            | 0        |
| Com_show_grants                     | 0        |
| Com_show_keys                       | 14       |
| Com_show_master_status              | 0        |
| Com_show_open_tables                | 0        |
| Com_show_plugins                    | 0        |
| Com_show_privileges                 | 0        |
| Com_show_procedure_code             | 0        |
| Com_show_procedure_status           | 0        |
| Com_show_processlist                | 8        |
| Com_show_profile                    | 0        |
| Com_show_profiles                   | 0        |
| Com_show_relaylog_events            | 0        |
| Com_show_replicas                   | 0        |
| Com_show_slave_hosts                | 0        |
| Com_show_replica_status             | 30092    |
| Com_show_slave_status               | 30092    |
| Com_show_status                     | 30117    |
| Com_show_storage_engines            | 0        |
| Com_show_table_status               | 0        |
| Com_show_tables                     | 14       |
| Com_show_triggers                   | 12       |
| Com_show_variables                  | 30109    |
| Com_show_warnings                   | 0        |
| Com_show_create_user                | 0        |
| Com_shutdown                        | 0        |
| Com_replica_start                   | 0        |
| Com_slave_start                     | 0        |
| Com_replica_stop                    | 0        |
| Com_slave_stop                      | 0        |
| Com_group_replication_start         | 0        |
| Com_group_replication_stop          | 0        |
| Com_stmt_execute                    | 12       |
| Com_stmt_close                      | 12       |
| Com_stmt_fetch                      | 0        |
| Com_stmt_prepare                    | 12       |
| Com_stmt_reset                      | 0        |
| Com_stmt_send_long_data             | 0        |
| Com_truncate                        | 1        |
| Com_uninstall_component             | 0        |
| Com_uninstall_plugin                | 0        |
| Com_unlock_instance                 | 0        |
| Com_unlock_tables                   | 1        |
| Com_update                          | 1318285  |
| Com_update_multi                    | 0        |
| Com_xa_commit                       | 0        |
| Com_xa_end                          | 0        |
| Com_xa_prepare                      | 0        |
| Com_xa_recover                      | 0        |
| Com_xa_rollback                     | 0        |
| Com_xa_start                        | 0        |
| Com_stmt_reprepare                  | 0        |
| Flush_commands                      | 3        |
| Handler_commit                      | 14922648 |
+-------------------------------------+----------+
174 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:30:17((none))]$

```


- prometheus
```json
mysql_global_status_commands_total{command="admin_commands", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_db", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_event", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_function", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_instance", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_procedure", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_resource_group", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_server", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_table", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_tablespace", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_user", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="alter_user_default_role", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="analyze", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="assign_to_keycache", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="begin", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="binlog", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="call_procedure", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="change_db", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="change_master", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="change_repl_filter", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="change_replication_source", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="check", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="checksum", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="clone", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="commit", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_db", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_event", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_function", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_index", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_procedure", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_resource_group", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_role", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_server", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_spatial_reference_system", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_table", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_trigger", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_udf", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_user", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="create_view", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="dealloc_sql", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="delete", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="delete_multi", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="do", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_db", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_event", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_function", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_index", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_procedure", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_resource_group", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_role", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_server", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_spatial_reference_system", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_table", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_trigger", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_user", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="drop_view", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="empty_query", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="execute_sql", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="explain_other", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="flush", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="get_diagnostics", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="grant", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="grant_roles", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="group_replication_start", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="group_replication_stop", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="ha_close", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="ha_open", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="ha_read", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="help", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="import", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="insert", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="insert_select", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="install_component", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="install_plugin", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="kill", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="load", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="lock_instance", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="lock_tables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="optimize", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="preload_keys", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="prepare_sql", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="purge", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="purge_before_date", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="release_savepoint", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="rename_table", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="rename_user", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="repair", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="replace", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="replace_select", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="replica_start", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="replica_stop", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="reset", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="resignal", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="restart", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="revoke", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="revoke_all", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="revoke_roles", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="rollback", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="rollback_to_savepoint", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="savepoint", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="select", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="set_option", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="set_password", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="set_resource_group", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="set_role", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_binlog_events", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_binlogs", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_charsets", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_collations", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_db", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_event", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_func", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_proc", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_table", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_trigger", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_create_user", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_databases", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_engine_logs", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_engine_mutex", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_engine_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_errors", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_events", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_fields", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_function_code", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_function_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_grants", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_keys", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_master_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_open_tables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_plugins", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_privileges", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_procedure_code", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_procedure_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_processlist", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_profile", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_profiles", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_relaylog_events", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_replica_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_replicas", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_slave_hosts", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_slave_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_storage_engines", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_table_status", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_tables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_triggers", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_variables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="show_warnings", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="shutdown", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="signal", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="slave_start", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="slave_stop", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_close", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_execute", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_fetch", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_prepare", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_reprepare", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_reset", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="stmt_send_long_data", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="truncate", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="uninstall_component", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="uninstall_plugin", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="unlock_instance", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="unlock_tables", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="update", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="update_multi", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_commit", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_end", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_prepare", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_recover", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_rollback", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_commands_total{command="xa_start", instance="172.17.130.48:9105", job="mysql_exporter"}

```








#### `mysql_global_status_handlers_total`

Total number of executed MySQL handlers

- SQL
```sql
[root@127.0.0.1:8032 18:25:09((none))]$ show global status like '%handler%';
+----------------------------+----------+
| Variable_name              | Value    |
+----------------------------+----------+
| Handler_commit             | 14922633 |
| Handler_delete             | 473013   |
| Handler_discover           | 0        |
| Handler_external_lock      | 23597963 |
| Handler_mrr_init           | 0        |
| Handler_prepare            | 5483124  |
| Handler_read_first         | 60645    |
| Handler_read_key           | 6225216  |
| Handler_read_last          | 0        |
| Handler_read_next          | 84473134 |
| Handler_read_prev          | 0        |
| Handler_read_rnd           | 478      |
| Handler_read_rnd_next      | 96362967 |
| Handler_rollback           | 41       |
| Handler_savepoint          | 0        |
| Handler_savepoint_rollback | 0        |
| Handler_update             | 966908   |
| Handler_write              | 75606756 |
+----------------------------+----------+
18 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:26:38((none))]$

```

- prometheus
```json
mysql_global_status_handlers_total{handler="commit", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="delete", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="discover", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="external_lock", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="mrr_init", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="prepare", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_first", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_key", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_last", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_next", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_prev", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_rnd", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="read_rnd_next", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="rollback", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="savepoint", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="savepoint_rollback", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="update", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_handlers_total{handler="write", instance="172.17.130.48:9105", job="mysql_exporter"}

```



#### `mysql_global_status_connection_errors_total`

Total number of MySQL connection errors


- SQL
```sql
[root@127.0.0.1:8032 18:17:38((none))]$ show global status like '%connection_errors%';
+-----------------------------------+-------+
| Variable_name                     | Value |
+-----------------------------------+-------+
| Connection_errors_accept          | 0     |
| Connection_errors_internal        | 0     |
| Connection_errors_max_connections | 0     |
| Connection_errors_peer_address    | 0     |
| Connection_errors_select          | 0     |
| Connection_errors_tcpwrap         | 0     |
+-----------------------------------+-------+
6 rows in set (0.01 sec)

[root@127.0.0.1:8032 18:25:09((none))]$

```

- prometheus
```json
mysql_global_status_connection_errors_total{error="accept", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_connection_errors_total{error="internal", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_connection_errors_total{error="max_connections", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_connection_errors_total{error="peer_address", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_connection_errors_total{error="select", instance="172.17.130.48:9105", job="mysql_exporter"}
mysql_global_status_connection_errors_total{error="tcpwrap", instance="172.17.130.48:9105", job="mysql_exporter"}

```

#### `mysql_global_status_buffer_pool_pages`
Innodb buffer pool pages by state

去掉total

`case "data", "free", "misc", "old":`
- SQL
```sql
[root@127.0.0.1:8032 18:12:15((none))]$ show global status like '%innodb_buffer_pool_pages%';
+----------------------------------+---------+
| Variable_name                    | Value   |
+----------------------------------+---------+
| Innodb_buffer_pool_pages_data    | 122867  |
| Innodb_buffer_pool_pages_dirty   | 0       |
| Innodb_buffer_pool_pages_flushed | 3001725 |
| Innodb_buffer_pool_pages_free    | 8194    |
| Innodb_buffer_pool_pages_misc    | 11      |
| Innodb_buffer_pool_pages_total   | 131072  |
+----------------------------------+---------+
6 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:17:38((none))]$

```

- prometheus
```json
mysql_global_status_buffer_pool_pages{instance="172.17.130.48:9105", job="mysql_exporter", state="data"}
mysql_global_status_buffer_pool_pages{instance="172.17.130.48:9105", job="mysql_exporter", state="free"}
mysql_global_status_buffer_pool_pages{instance="172.17.130.48:9105", job="mysql_exporter", state="misc"}

```


#### `mysql_global_status_buffer_pool_dirty_pages`
`case "dirty"`
- SQL
```sql
[root@127.0.0.1:8032 18:12:15((none))]$ show global status like '%innodb_buffer_pool_pages%';
+----------------------------------+---------+
| Variable_name                    | Value   |
+----------------------------------+---------+
| Innodb_buffer_pool_pages_data    | 122867  |
| Innodb_buffer_pool_pages_dirty   | 0       |
| Innodb_buffer_pool_pages_flushed | 3001725 |
| Innodb_buffer_pool_pages_free    | 8194    |
| Innodb_buffer_pool_pages_misc    | 11      |
| Innodb_buffer_pool_pages_total   | 131072  |
+----------------------------------+---------+
6 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:17:38((none))]$

```

- prometheus
```json
mysql_global_status_buffer_pool_dirty_pages{instance="172.17.130.48:9105", job="mysql_exporter"}

```

#### `mysql_global_status_buffer_pool_page_changes_total`
`default`
- SQL
```sql
[root@127.0.0.1:8032 18:12:15((none))]$ show global status like '%innodb_buffer_pool_pages%';
+----------------------------------+---------+
| Variable_name                    | Value   |
+----------------------------------+---------+
| Innodb_buffer_pool_pages_data    | 122867  |
| Innodb_buffer_pool_pages_dirty   | 0       |
| Innodb_buffer_pool_pages_flushed | 3001725 |
| Innodb_buffer_pool_pages_free    | 8194    |
| Innodb_buffer_pool_pages_misc    | 11      |
| Innodb_buffer_pool_pages_total   | 131072  |
+----------------------------------+---------+
6 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:17:38((none))]$

```

- prometheus
```json
mysql_global_status_buffer_pool_page_changes_total{instance="172.17.130.48:9105", job="mysql_exporter", operation="flushed"}

```
#### `mysql_global_status_innodb_row_ops_total`

- SQL
```sql
[root@127.0.0.1:8032 18:05:13((none))]$ show global status like '%innodb_rows%';
+----------------------+----------+
| Variable_name        | Value    |
+----------------------+----------+
| Innodb_rows_deleted  | 472812   |
| Innodb_rows_inserted | 12751547 |
| Innodb_rows_read     | 88766551 |
| Innodb_rows_updated  | 945652   |
+----------------------+----------+
4 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:05:14((none))]$

```

- prometheus
```json
mysql_global_status_innodb_row_ops_total{instance="172.17.130.48:9105", job="mysql_exporter", operation="deleted"}
mysql_global_status_innodb_row_ops_total{instance="172.17.130.48:9105", job="mysql_exporter", operation="inserted"}
mysql_global_status_innodb_row_ops_total{instance="172.17.130.48:9105", job="mysql_exporter", operation="read"}
mysql_global_status_innodb_row_ops_total{instance="172.17.130.48:9105", job="mysql_exporter", operation="updated"}


```
#### `mysql_global_status_performance_schema_lost_total`

- SQL

```sql
[root@127.0.0.1:8032 18:05:14((none))]$ show global status like '%performance_schema%';
+-------------------------------------------------------+-------+
| Variable_name                                         | Value |
+-------------------------------------------------------+-------+
| Performance_schema_accounts_lost                      | 0     |
| Performance_schema_cond_classes_lost                  | 0     |
| Performance_schema_cond_instances_lost                | 0     |
| Performance_schema_digest_lost                        | 0     |
| Performance_schema_file_classes_lost                  | 0     |
| Performance_schema_file_handles_lost                  | 0     |
| Performance_schema_file_instances_lost                | 0     |
| Performance_schema_hosts_lost                         | 0     |
| Performance_schema_index_stat_lost                    | 0     |
| Performance_schema_locker_lost                        | 0     |
| Performance_schema_memory_classes_lost                | 0     |
| Performance_schema_metadata_lock_lost                 | 0     |
| Performance_schema_mutex_classes_lost                 | 0     |
| Performance_schema_mutex_instances_lost               | 0     |
| Performance_schema_nested_statement_lost              | 0     |
| Performance_schema_prepared_statements_lost           | 0     |
| Performance_schema_program_lost                       | 0     |
| Performance_schema_rwlock_classes_lost                | 0     |
| Performance_schema_rwlock_instances_lost              | 0     |
| Performance_schema_session_connect_attrs_longest_seen | 122   |
| Performance_schema_session_connect_attrs_lost         | 0     |
| Performance_schema_socket_classes_lost                | 0     |
| Performance_schema_socket_instances_lost              | 0     |
| Performance_schema_stage_classes_lost                 | 0     |
| Performance_schema_statement_classes_lost             | 0     |
| Performance_schema_table_handles_lost                 | 0     |
| Performance_schema_table_instances_lost               | 0     |
| Performance_schema_table_lock_stat_lost               | 0     |
| Performance_schema_thread_classes_lost                | 0     |
| Performance_schema_thread_instances_lost              | 0     |
| Performance_schema_users_lost                         | 0     |
+-------------------------------------------------------+-------+
31 rows in set (0.00 sec)

[root@127.0.0.1:8032 18:12:15((none))]$

```

- prometheus

```json

mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="accounts_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="cond_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="cond_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="digest_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="file_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="file_handles_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="file_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="hosts_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="index_stat_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="locker_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="memory_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="metadata_lock_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="mutex_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="mutex_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="nested_statement_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="prepared_statements_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="program_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="rwlock_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="rwlock_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="session_connect_attrs_longest_seen", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="session_connect_attrs_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="socket_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="socket_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="stage_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="statement_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="table_handles_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="table_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="table_lock_stat_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="thread_classes_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="thread_instances_lost", job="mysql_exporter"}
mysql_global_status_performance_schema_lost_total{instance="172.17.130.48:9105", instrumentation="users_lost", job="mysql_exporter"}
```




## show global variables



#### `mysql_global_variables_new`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```

#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```



#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```



#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```



#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```


#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```



#### `mysql_global_status_buffer_pool_dirty_pages`

- SQL
```sql

```

- prometheus
```json

```










