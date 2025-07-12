---
title: 任务过期
description: task_expire
date: 2023-10-24
weight: 70000


---

{{< alert >}}

- [任务过期](http://10.5.58.182/#/migration/sync)

- [任务过期](https://bbkv6krkep.feishu.cn/docx/NgNedCM5aomoJzxFl8kcWndBnsh)

- [dts手册](https://bbkv6krkep.feishu.cn/wiki/Fd0aw0s5Fi8TxFkakkacDoFzncb)


{{< /alert >}}


一、问题概述

问题原因：

解决办法：

二、问题背景：

所属环境：

问题现象：

三、问题处理步骤：

四、问题处理：




### 页面报错
{{<figure src="/images/mysql/1000-work/dts/task_expire.png">}}





### 报错日志：

> `flink-root-taskexecutor-0-dts182.log`
>
```sql
[root@dts182 log]# grep -i 'Caused' /data/greatdts/sync/log/flink-root-taskexecutor-0-dts182.log.5
Caused by: java.sql.BatchUpdateException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.BatchUpdateException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.BatchUpdateException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Got error when refresh table info. Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.BatchUpdateException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.BatchUpdateException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist
Caused by: java.sql.SQLSyntaxErrorException: Table 'paasx_flow.paas_external_completed_task' doesn't exist

```


### 核对源端与目标端的paasx_flow.paas_external_completed_task表是否存在
```sql
desc paasx_flow.paas_external_completed_task;

use paasx_flow;show table status like 'paas_external_completed_task';
```
> 经过核对，源端存在该表，目标端不存在该表
>
> **结论：当前DTS还不支持ddl语句同步**




### 数据节点对象列表信息

{{<figure src="/images/mysql/1000-work/dts/task_expire_list.png">}}

### 任务配置，通过查看日志找：/data/greatdts/sync/log/flink-root-taskexecutor-0-dts182.log.5
```yaml
bigint.unsigned.handling.mode = precise
binary.handling.mode = bytes
connect.keep.alive = true
connector.class = io.debezium.connector.mysql.MySqlConnector
database.history = io.debezium.relational.history.FileDatabaseHistory
database.history.file.dir = /data/greatdts/dts-cc/../../var/history/TASK-HISTORY-50
database.history.file.filename = /data/greatdts/dts-cc/../../var/history/TASK-HISTORY-50.dat
database.history.skip.unparseable.ddl = true
database.history.store.only.captured.tables.ddl = true
database.hostname = 10.5.58.179
database.include.list = paasx_flow,paasx
database.password = ********
database.port = 16315
database.server.name = MySQLSource
database.user = admin
include.schema.changes = false
inconsistent.schema.handling.mode = fail
max.batch.size = 2048
max.queue.size = 8192
max.queue.size.in.bytes = 0
name = MySQLSource
offset.flush.interval.ms = 0
offset.flush.timeout.ms = 5000
offset.storage = com.greatsync.extend.kafka.storage.FileOffsetBackingStore
offset.storage.file.dir = /data/greatdts/dts-cc/../../var/offset/TASK-OFFSET-50
offset.storage.file.filename = /data/greatdts/dts-cc/../../var/offset/TASK-OFFSET-50.dat
offset.storage.value = {"sourcePartition":{"server":"MySQLSource"},"sourceOffset":{"file":"binlog.000040","gtids":"1db06c77-65eb-11ee-bc53-0cda411db5b3:1-1225580,1db4acc0-65eb-11ee-9b60-0cda411d9ae7:1-7573835"}}
provide.transaction.metadata = false
sanitize.field.names = true
snapshot.locking.mode = none
snapshot.mode = schema_only_recovery
table.include.list = paasx_flow.*,paasx.A4_USER,paasx.CWY_DJLX_YWKM,paasx.CWY_HL,paasx.CWY_JSFS,paasx.CWY_YHZD,paasx.CWY_YHZH,paasx.CWY_YWKM,paasx.CWY_YWLX,paasx.SUPPLY_BASIC,paasx.ZSJ_GJ,paasx.ZSJ_JLDW,paasx.ZSJ_JRJG,paasx.ZSJ_SS,paasx.ZSJ_WZSB,paasx.ZSJ_WZSBFL,paasx.ZSJ_ZCURRENCY,paasx.biz_notice,paasx.caster_visual,paasx.caster_visual_app,paasx.caster_visual_background,paasx.caster_visual_category,paasx.caster_visual_config,paasx.caster_visual_map,paasx.contract_middle_menu,paasx.contract_middle_qx_tb_status,paasx.contract_middle_role,paasx.contract_middle_role_menu,paasx.contract_middle_user_dept_role,paasx.contract_middle_user_role,paasx.insurance_info,paasx.jt_oa_post_file_info,paasx.jt_oa_post_info,paasx.nacos_config_info,paasx.old_ck,paasx.old_fbfx,paasx.old_gyjs,paasx.old_kc,paasx.old_syjs,paasx.open_operate,paasx.paas_app_info,paasx.paas_app_ios,paasx.paas_app_resource,paasx.paas_callback,paasx.paas_client,paasx.paas_client_integration,paasx.paas_code,paasx.paas_custom_component,paasx.paas_custom_form,paasx.paas_datasource,paasx.paas_dept,paasx.paas_dept_ini,paasx.paas_dept_paas,paasx.paas_dept_role,paasx.paas_devpos_harbor,paasx.paas_devpos_job,paasx.paas_devpos_template,paasx.paas_dict,paasx.paas_dict_biz,paasx.paas_error_info,paasx.paas_gitlab_jar,paasx.paas_gitlab_library,paasx.paas_individuation,paasx.paas_information,paasx.paas_information_log,paasx.paas_jot_down,paasx.paas_labor_role,paasx.paas_labor_role_menu,paasx.paas_labor_user,paasx.paas_labor_user_role,paasx.paas_log_api,paasx.paas_log_error,paasx.paas_log_usual,paasx.paas_machine,paasx.paas_menu,paasx.paas_midware_agent,paasx.paas_midware_instance,paasx.paas_msg_template,paasx.paas_notice,paasx.paas_notice_read_log,paasx.paas_notifications_message,paasx.paas_online_data,paasx.paas_online_data_option,paasx.paas_online_template,paasx.paas_oss,paasx.paas_oss_file,paasx.paas_param,paasx.paas_platform_portal,paasx.paas_process_leave,paasx.paas_registry,paasx.paas_release_version,paasx.paas_role,paasx.paas_role_menu,paasx.paas_role_scope,paasx.paas_route,paasx.paas_rule_menu,paasx.paas_rule_role,paasx.paas_scope_api,paasx.paas_scope_data,paasx.paas_service_client,paasx.paas_service_config,paasx.paas_service_register,paasx.paas_sms,paasx.paas_tenant,paasx.paas_tenant_client,paasx.paas_top_menu,paasx.paas_top_menu_setting,paasx.paas_update_log,paasx.paas_user,paasx.paas_user_dept,paasx.paas_user_dept_role,paasx.paas_user_role,paasx.pass_msg_log,paasx.pc_module_approve_user,paasx.pc_module_info,paasx.pc_scheme_approve_user,paasx.pc_scheme_info,paasx.project_material_coding,paasx.sc_xm_project,paasx.sm_construction_worker,paasx.sm_project_info,paasx.sneb_organ,paasx.sneb_role,paasx.sneb_role2user,paasx.sneb_sync_error_log,paasx.sneb_sync_version,paasx.sneb_tb_contract,paasx.sneb_tb_project,paasx.sneb_user,paasx.sneb_user2organ,paasx.tb_project,paasx.tech_program_del_log,paasx.tech_program_list,paasx.tech_project

```
> `table.include.list = paasx_flow.*,paasx.A4_USER,` 表明同步了源端的paasx_flow库下面的所有表，而数据节点的对象列表里面并没有配置这个
>
> 结论：任务配置内容与数据库节点的配置对象列表信息不一致

{{<warning>}}

1. 需要用新的对象列表的话，最好是新增一个，不要改原来的
2. 产品需要改进，任务配置信息需要与数据库节点的配置对象列表信息一致
{{</warning>}}

