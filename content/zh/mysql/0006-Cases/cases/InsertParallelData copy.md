---
title: 大量数据
date: 2023-10-12
description: >
  开发规范：insert语句批量插入的数据行不超过1000.


weight: 60000
---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


## 一、环境架构



{{< alert title="配置信息">}}

| 硬件名称      | 配置      |
|:--------------|:----------|
| Arch          | aarch64   |
| OS            | Kylin-v10 |
| CPU           | 64C       |
| Memory        | 512G      |
| Space         | 14T       |
| MySQL Version | 8.0.26    |

{{< /alert >}}

## 二、问题现象



```sql
create table asia_time_deposit_info
(impr_data_dt datetime not null,
cst_id char(18) character set utf8mb4 collate utf8mb4_bin not null,
ccy_eng_cd char(3) character set utf8mb4 collate utf8mb4_bin default null,
trm_depseq decimal(8,0) default null,
ovsea_dep_acc_tpcd char(2) character set utf8mb4 collate utf8mb4_bin default null,
acc_id varchar(40) character set utf8mb4 collate utf8mb4_bin not null,
bal  decimal(25,8) default null,
bwb_amt_bal decimal(21,4) default null,
intrt_val decimal(10,6) default null,
exp_acr_int_amt decimal(31,16) default null,
toat decimal(19,8) default null,
intsetl_int_amt decimal(17,5) default null,
dep_int_amt decimal(17,5) default null,
valdt datetime default null,
exdt datetime default null,
rnw_ind char(1) character set utf8mb4 collate utf8mb4_bin default null,
multi_tenancy_id char(5) character set utf8mb4 collate utf8mb4_bin not null,
ri_id varchar(120) character set utf8mb4 collate utf8mb4_bin not null,
primary key(impr_data_dt,cst_id,acc_id,multi_tenancy_id,ri_id)
)engine=innodb default charset=utf8mb4 collate=utf8mb4_bin
partition by range columns(impr_data_dt)
(
partition p20221031 values less than ('2022-11-01 00:00:00')engine=innodb,
partition p20221130 values less than ('2022-12-01 00:00:00')engine=innodb,
partition p20221231 values less than ('2023-01-01 00:00:00')engine=innodb,
partition p20230131 values less than ('2023-02-01 00:00:00')engine=innodb,
partition p20230227 values less than ('2023-03-01 00:00:00')engine=innodb,
partition p20230331 values less than ('2023-04-01 00:00:00')engine=innodb,
partition p20230430 values less than ('2023-05-01 00:00:00')engine=innodb,
partition p20230501 values less than ('2023-05-02 00:00:00')engine=innodb,
partition p20230502 values less than ('2023-05-03 00:00:00')engine=innodb,
partition p20230503 values less than ('2023-05-04 00:00:00')engine=innodb,
partition p20230504 values less than ('2023-05-05 00:00:00')engine=innodb,
partition p20230505 values less than ('2023-05-06 00:00:00')engine=innodb,
partition p20230506 values less than ('2023-05-07 00:00:00')engine=innodb,
partition p20230507 values less than ('2023-05-08 00:00:00')engine=innodb,
partition p20230508 values less than ('2023-05-09 00:00:00')engine=innodb,
partition p20230509 values less than ('2023-05-10 00:00:00')engine=innodb,
partition p20230510 values less than ('2023-05-11 00:00:00')engine=innodb
);


```


## 三、影响范围

该问题暂时对业务无影响。


## 四、问题分析过程

### （1）问题发生时间
问题发生时间段为2023-10-12 14:26:00至2023-10-12 14:28:00，持续2分钟。

### （2）问题处理过程

1. 在收到告警后，登录数据库后台，查看当前数据库状态。经初步确认，没有发现高耗时的SQL。与报障人员确认后，初步判断问题可能源于历史高消耗SQL，导致了 CPU 资源的激增。

2. 接着查看慢查询日志，分析发现这个时间段产生了慢SQL语句且平均执行时间在20s左右，而在平常该类型语句平均耗时3s以内，因此该时间段内的慢查询是因资源无法满足所致，如下图所示

3. 随后，通过分析该时间段内的binlog日志，发现存在高并发批次插入大量数据行的SQL语句，一次insert数据量最高达到86万条，如下图

4. 根据上述输出信息，我们可以得出以下结论：高并发批次插入大量数据，导致数据库性能下降，进而出现大量慢查询。

## 五、问题原因分析
{{< alert title="原因">}}

N_ISAG_BB系统CPU使用激增问题由高并发批次插入大量数据，导致数据库性能下降出现大量慢查询，并发线程堆积使得CPU使用率激增，innodb层的并发线程处理被慢查询阻塞。

{{< /alert >}}

## 六、解决问题
{{< alert title="建议">}}

针对高并发批次插入大量数据导致优化选择错误的执行计划的问题，给出如下建议：

- 请应用侧遵循开发规范，insert语句批量插入的数据行不超过1000。

{{< /alert >}}



































