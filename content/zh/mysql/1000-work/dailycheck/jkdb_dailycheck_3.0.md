---
title: jkdb_dailycheck_3.0.sh
description: jkdb_dailycheck_3.0.sh
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}



```bash
#!/bin/bash
# DATE 2024-01-15
# VERSION 3.0
# DESC 日常巡检
# ADD
# 1、新增统计访问锁信息
# 2、新增统计执行异常的语句
# 3、统计死锁信息
# 4、统计全表扫描


### 变量定义 ###
source ~/.bash_profile
tool_dir="/home/db/mysql/scripts/dailycheck"
tool_history_dir="${tool_dir}/history"
conf_dir="${tool_dir}/conf"
conf_file="${conf_dir}/jkdb_dailycheck.conf"
output_dir="${tool_dir}/output"
log_date_time=`date +%Y%m%d%H%M%S`
host_name=`hostname`
output_file="${output_dir}/DailyCheckV2.0__${host_name}__ticbase__${log_date_time}.out"
checkout_file="${tool_history_dir}/DailyCheck_${log_date_time}.log"
date_time="date +'%F_%T'"
cluster_home_dir="/data"
cluster_core_dir="/home/backupfile"
cluser_data_dir="/data/mysqldata/*/dbdata"

### 前置条件 ###
# 检查数据库服务是否在线
db_check=`ps -ef|grep mysqld|grep mysql.sock|awk -F '=' '{print $NF}'`
[ -z ${db_check} ] && { echo "数据库实例不在线，脚本退出！"|tee -a ${output_file};exit 1; }

# 检查调度节点是否在线
router_check=`ps -ef|grep dbscale.conf|grep config-file |awk '{print $2}'`
[ -z ${router_check} ] && { echo "调度节点不在线，脚本退出！"|tee -a ${output_file};exit 1; }

# 检查是否配置login path
mysql_config_editor print --all 2>/dev/null|grep -iqE "mylogin|mylogin_local"  || { echo "未配置login path";exit 1; }
mylogin_local="mysql --login-path=mylogin_local"
mylogin="mysql --login-path=mylogin"

# 检查数据节点角色
db_role_sql="show variables like 'rpl_semi_%_enabled';"
db_role=`${mylogin_local} -sse "${db_role_sql}"|egrep -iE "ON|1"|awk '{print $1}'|awk -F '_' '{print $4}'`
[ -z $db_role ] && { echo "数据节点未分配角色";exit 1;}

### sql语句集 ###
# 获取表空间数量
tablespace_nums_sql="select count(*) from information_schema.innodb_tablespaces;"

# 主从复制状态量
slave_host_num_sql="show slave hosts;"
slave_status_sql="show slave status\G"
binlog_dump_num_sql="select count(*) from information_schema.processlist where command like 'Binlog Dump%';"

# 连接状态量
conn_num_sql="select if(round((count(*)/(select @@max_connections))*100)<1,1,round((count(*)/(select @@max_connections))*100)) from information_schema.processlist;"
thread_running_num_sql="show global status like 'Threads_running';"

# 事务状态量
long_transaction_num_sql="select count(*) from information_schema.innodb_trx isi,information_schema.processlist isp where isi.trx_mysql_thread_id=isp.id and (isi.trx_lock_structs > 5 or isi.trx_rows_locked >100 or isi.trx_rows_modified > 10000 or to_seconds(now()) - to_seconds(isi.trx_started) > 10);"
long_transaction_sql="select isi.*,isp.* from information_schema.innodb_trx isi,information_schema.processlist isp where isi.trx_mysql_thread_id=isp.id and (isi.trx_lock_structs > 5 or isi.trx_rows_locked >100 or isi.trx_rows_modified > 10000 or to_seconds(now()) - to_seconds(isi.trx_started) > 10)\G"


# 集群数据节点数量
topol_status_sql="dbscale show dataservers;"

# 非innodb表数量
no_innodb_engine_table_sql="select concat(table_schema,'.',table_name) as tb,engine  from information_schema.tables where engine != 'innodb'and table_schema not in('mysql','sys','information_schema','performance_schema');"

#无主键表数量
no_primary_innodb_table_sql="select concat(a.table_schema,'.',a.table_name) from (select table_schema,table_name from information_schema.tables where table_schema not in('mysql','information_schema','performance_schema','sys','dbscale') and table_type='BASE TABLE') as a left join (select table_schema,table_name from information_schema.table_constraints where constraint_type='PRIMARY KEY' and table_schema not in('mysql','information_schema','performance_schema','sys','dbscale')) as b on a.table_schema=b.table_schema and a.table_name=b.table_name where b.table_name is null;"

# 锁定的用户数量
user_lock_sql="select user from mysql.user where account_locked='Y' and user not in ('mysql.session','mysql.sys','mysql.infoschema');"

# 数据库日志开关
mysqld_log_status_sql="show variables like 'general_log';"

# 调度节点debug日志开关
dbscale_log_level_sql="dbscale show options like 'log-level';"

# 统计访问锁信息
access_lock_info_num_sql="select count(*) from performance_schema.events_statements_history where sql_text like '%lock%' and thread_id != ps_current_thread_id();"
access_lock_info_sql="select thread_id,date_sub(now(), interval (select variable_value from performance_schema.global_status where variable_name='uptime') - timer_start*10e-13 second) as timer_start,date_sub(now(), interval (select variable_value from performance_schema.global_status where variable_name='uptime') - timer_end*10e-13 second) as   timer_end,sql_text,errors, message_text from performance_schema.events_statements_history where sql_text like '%lock%' and thread_id != ps_current_thread_id();"

# 统计执行异常的语句
exec_error_num_sql="select count(*) from sys.statements_with_errors_or_warnings where errors>0;"
exec_error_sql="select * from sys.statements_with_errors_or_warnings where errors>0;"

# 统计全表扫描
full_table_scan_num_sql="select count(*) from sys.statements_with_full_table_scans where db not in('mysql','information_schema','sys','performance_schema');"
full_table_scan_sql="select * from sys.statements_with_full_table_scans where db not in('mysql','information_schema','sys','performance_schema');"

# 统计死锁信息
dead_lock_sql="show engine innodb status\G"

# 检查数据目录
[ -d ${tool_dir} ] || { mkdir -p ${tool_dir}; }
[ -d ${tool_history_dir} ] || { mkdir -p ${tool_history_dir}; }
[ -d ${output_dir} ] || { mkdir -p ${output_dir}; }
[ -d ${conf_dir} ] || { mkdir -p ${conf_dir}; }

### 生成监控项开关 ###
if [ ! -f ${conf_file} ] || [ ! -s ${conf_file} ];then
cat >${conf_file}<<eof
# 服务器运行时长
sys_uptime_check=on

# 检查数据库实例进程数量
mysql_proc_check=on

# zookeeper进程数量
zook_proc_check=on

# router进程数量
dbscale_proc_check=on

# 在过去24H内,数据库是否重启过
mysql_restart_check=on

# Linux用户口令过期时间
password_expire_date_check=on

# 主机内存资源使用率
sys_mem_usage_check=on

# 主机交换空间使用率
sys_swap_usage_check=on

# 主机CPU资源使用率
sys_cpu_usage_check=on

# 数据目录使用率
cluster_home_dir_usage_check=on

# 数据库文件大小
cluster_data_file_size_check=on

# 数据目录INODE使用率
cluster_home_dir_inode_usage_check=on

# 数据库表空间数量
mysql_tablesapce_nums=on

# 数据库半同步复制状态
mysql_repl_check=on

# 数据库半同步复制延迟
mysql_repl_time_check=on

# 数据库GTID
mysql_gtid_check=on

# 数据库Master节点dump线程
master_binlog_dump_check=on

# 数据库从节点I/O线程
slave_io_thread_status_check=on

# 数据库从节点SQL线程
slave_sql_thread_status_check=on

# 数据库连接池使用率
mysql_conn_usage_check=on

# 数据库活跃会话数
mysql_thread_running_check=on

# 数据库大事务
mysql_long_transaction_check=on

# 集群拓扑状态
cluster_topol_status_check=on

# 非innodb引擎表
not_innodb_engine_table=on

# 无主键的InnoDB表
no_primary_innodb_table_check=on

# 用户锁定
mysql_user_lock_check=on

# 慢日志大小
mysql_slowlog_size_check=on

# 普通日志
mysqld_general_log_check=on

# debug日志
dbscale_debug_log_check=on

# 备份任务检查
rds_backup_schedule_check=on

# 统计访问锁信息
mysql_access_lock_info_check=on

# 统计执行异常的语句
mysql_exec_error_check=on

# 统计全表扫描
mysql_full_table_scan_check=on

# 统计死锁信息
msyql_dead_lock_check=on
eof
fi

# 格式化输出结果
echo "`eval ${date_time}` 开始执行巡检"
printf "%s\t ||%s\t ||%s\t ||%s\t ||%s\t ||%s\n" 检查类别 检查项 检查对象 检查结果 检查值 检查阈值|expand -t 26 |tee -a ${output_file}

function printf_cmd(){
                printf "%s\t ||%s\t ||%s\t ||%s\t ||%s\t ||%s\n" $* | expand -t 26
}

# 服务器运行时长检查
### 检查服务器主机重启 ###
function sys_uptime_check()
{
check_entry="服务器运行时长"
if cat ${conf_file}|grep -v "^#"|grep sys_uptime_check|grep -qi on;then
        current_time=$(date -d "`date`" +%s)
        reboot_time=$(date -d "`uptime -s`" +%s)
        diff_time=$(($current_time - $reboot_time))
        if [ $diff_time -gt 86400 ];then
                result="正常"
				check_values=0
				compare_symbol="大于"
        else
                result="异常"
				check_values=1
				compare_symbol="小于"
        fi
        printf_cmd 运行状态 ${check_entry} 操作系统 ${result} ${diff_time}秒 大于86400秒|tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}24小时],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
sys_uptime_check

# 进程服务检查
### 检查数据库实例进程数量 ###
function mysql_proc_check()
{
check_entry="数据库实例进程数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_proc_check|grep -qi on;then
        proc_num=`ps -ef|grep mysqld|grep mysql.sock|wc -l`
        if [ ${proc_num} -eq 1 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"

        else
                proc_num=0
                result="异常"
				check_values=1
				compare_symbol="不等于"
        fi
        printf_cmd 运行状态 ${check_entry} 数据库 ${result} ${proc_num}个 1个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}1个],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_proc_check

### zookeeper进程数量检查 ###
function zook_proc_check()
{
check_entry="zookeeper进程数量"
if cat ${conf_file}|grep -v "^#"|grep zook_proc_check|grep -qi on;then
        proc_num=`ps -ef|grep zookeeper |grep -v grep|wc -l`
        if [ ${proc_num} -eq 1 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"
        else
                result="异常"
				check_values=1
				compare_symbol="不等于"
        fi
        printf_cmd 运行状态 ${check_entry} 应用 ${result} ${proc_num}个 1个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[应用],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}1个],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time} `${check_entry}，跳过检查" >>${checkout_file}
fi
}
zook_proc_check

### dbscale进程数量检查 ###
function dbscale_proc_check()
{
check_entry="router进程数量"
if cat ${conf_file}|grep -v "^#"|grep  dbscale_proc_check|grep -qi on;then
        proc_num=`ps -ef|grep dbscale.conf|grep config-file|wc -l`
        if [ ${proc_num} -eq 1 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"
        else
                result="异常"
				check_values=1
				compare_symbol="不等于"
        fi
        printf_cmd 运行状态 ${check_entry} 应用 ${result} ${proc_num}个 1个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[应用],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}1个],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
dbscale_proc_check

### 在过去24H内，检查数据库是否重启过 ###
function mysql_restart_check()
{
check_entry="数据库实例运行时长"
if cat ${conf_file}|grep -v "^#"|grep  mysql_restart_check|grep -qi on;then
        uptime_seconds=`${mylogin_local} -sse "show global status like 'uptime';"|awk '{print $2}'`
        uptime_hour=`${mylogin_local} -sse "select round('${uptime_seconds}'/60/60);"`
        if [ ${uptime_hour} -gt 24 ];then
                result="正常"
				check_values=0
				compare_symbol="大于"
        else
                result="异常"
				check_values=1
				compare_symbol="小于"
        fi
        printf_cmd 运行状态 ${check_entry} 数据库 ${result} ${uptime_hour}小时 大于24小时|tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}24小时],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_restart_check

### Linux用户口令过期时间 ###
function password_expire_date_check()
{
check_entry="Linux用户口令过期时间"
if cat ${conf_file}|grep -v "^#"|grep password_expire_date_check|grep -qi on;then
        today_seconds=`date +%s`
        last_change_seconds=`chage -l mysql|grep -i last|awk -F ": " '{print $2}'|xargs -i{} date -d "{}" +%s`
        let diff_days=(${today_seconds}-${last_change_seconds})/24/3600
        max_days_betwwen_change=`chage -l mysql|grep -w Maximum|awk -F": " '{print $2}'`
        if [ ${max_days_betwwen_change} -gt 0 ];then
                let next_expire_before_days=${max_days_betwwen_change}-${diff_days}
                        if [ ${next_expire_before_days} -gt 5 ];then
                                result="正常"
								check_values=0
								compare_symbol="大于"
                        else
                                result="异常"
								check_values=1
								compare_symbol="小于"
                        fi
        else
                next_expire_before_days=99999
                result="正常"
				check_values=0
				compare_symbol="大于"
                echo "`eval ${date_time}` 口令永不过期" >>${checkout_file}
        fi
        printf_cmd 运行状态 ${check_entry} 操作系统 ${result} ${next_expire_before_days}天 大于5天 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}5天],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
password_expire_date_check

# 主机资源检查
### 主机内存资源占用检查 ###
function sys_mem_usage_check()
{
check_entry="主机内存使用率"
if cat ${conf_file}|grep -v "^#"|grep sys_mem_usage_check|grep -qi on;then
        #mem_usage_percentage=`free -m|grep -i mem|awk '{printf "%i%s\n",$3/$2*100,"%"}'`
        mem_usage_percentage=`free -m|grep -i mem|awk '{printf "%i\n",$3/$2*100}'`
        output=`echo ${mem_usage_percentage}|awk '{if($1 < 70) print $1}'`
		if [ ! -z ${output} ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 主机资源 ${check_entry} 操作系统 ${result} ${mem_usage_percentage}% 小于70% |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}70%],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
sys_mem_usage_check

### 主机交换空间资源占用检查 ###
function sys_swap_usage_check()
{
check_entry="主机交换空间使用率"
if cat ${conf_file}|grep -v "^#"|grep sys_swap_usage_check|grep -qi on;then
        swap_usage_percentage=`free -b|grep -i swap|awk '{printf "%i\n",$3/$2*100}'`
        output=`echo ${swap_usage_percentage}|awk '{if($1 < 10) print $1}'`
		if [ ! -z ${output} ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 主机资源 ${check_entry} 操作系统 ${result} ${swap_usage_percentage}% 小于10% |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}10%],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
sys_swap_usage_check


### 主机CPU资源占用 ###
function sys_cpu_usage_check()
{
check_entry="主机CPU使用率"
if cat ${conf_file}|grep -v "^#"|grep sys_cpu_usage_check|grep -qi on;then
    interval_time=5
        last_cpu_info=`cat /proc/stat | grep -w cpu | awk '{for(i=2;i<9;i=i+1){printf $i " "};printf "\n"}'`
        last_sys_idle_time=`echo ${last_cpu_info} | awk '{print $4}'`
        last_total_cpu_time=`echo ${last_cpu_info}|awk 'BEGIN{sum=0}{for(i=1;i<8;i++){sum+=$i}}END{print sum}'`
        sleep ${interval_time}
        next_cpu_info=`cat /proc/stat | grep -w cpu | awk '{for(i=2;i<9;i=i+1){printf $i " "};printf "\n"}'`
        next_sys_idle_time=`echo ${next_cpu_info} | awk '{print $4}'`
        next_total_cpu_time=`echo ${next_cpu_info}|awk 'BEGIN{sum=0}{for(i=1;i<8;i++){sum+=$i}}END{print sum}'`
        # CPU空闲时长
        system_idle_time=`echo "${last_sys_idle_time} ${next_sys_idle_time}"|awk '{print ($2-$1)}'`
        # CPU总时长
        system_total_time=`echo "${last_total_cpu_time} ${next_total_cpu_time}"|awk '{print ($2-$1)}'`
        # CPU使用率
        #cpu_usage=`echo "${system_idle_time} ${system_total_time}"|awk '{printf "%.2f%s",100-$1/$2*100,"%"}'`
        cpu_usage=`echo ${system_idle_time} ${system_total_time}|awk '{printf "%.2f",100-$1/$2*100}'`
        output=`echo ${cpu_usage} |awk '{if($1 < 50) print $1}'`
		if [ ! -z ${output} ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 主机资源 ${check_entry} cpu ${result} ${cpu_usage}% 小于50% |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}50%],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
sys_cpu_usage_check

### 数据目录使用率 ###
function cluster_home_dir_usage_check()
{
check_entry="数据目录使用率"
if cat ${conf_file}|grep -v "^#"|grep cluster_home_dir_usage_check|grep -qi on;then
        disk_usage=`df -h ${cluster_home_dir}|grep -i "/dev"|awk '{print $(NF-1)}'|sed 's/%//'`
        output=`echo ${disk_usage}|awk '{if($1 < 50) print $1}'`
		if [ ! -z ${output} ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 主机资源 ${check_entry} 操作系统 ${result} ${disk_usage}% 小于50% |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}50%],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${datetime}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
cluster_home_dir_usage_check

### 数据库文件大小 ###
function cluster_data_file_size_check()
{
check_entry="数据库文件大小"
if cat ${conf_file}|grep -v "^#"|grep cluster_data_file_size_check|grep -qi on;then
		cd ${cluser_data_dir}
        data_size=`ls|grep -vE "bin|relay"|xargs -n1 du -sb |awk '{tot+=$1};END{printf "%i\n",tot/1024/1024}'`
		if [ ${data_size} -lt 1048576 ];then
        result="正常"
		else
		result="异常"
		fi
        printf_cmd 主机资源 ${check_entry} 操作系统 ${result} ${data_size}Mb 1048576Mb |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[2],检查结果[],当前值[${data_size}],结果描述[${check_entry}${data_size}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${datetime}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
cluster_data_file_size_check

### 数据目录INODE使用率 ###
function cluster_home_dir_inode_usage_check()
{
check_entry="数据目录inode使用率"
if cat ${conf_file}|grep -v "^#"|grep cluster_home_dir_inode_usage_check|grep -qi on;then
        inode_usage=`df -ihP ${cluster_home_dir}|grep "/dev"|awk '{print $(NF-1)}'|sed 's/%//'`
        output=`echo ${inode_usage}|awk '{if($1 < 50) print $1}'`
		if [ ! -z ${output} ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
                printf_cmd 主机资源 ${check_entry} 操作系统 ${result} ${inode_usage}% 小于50% |tee -a ${output_file}
				echo "检查项[${check_entry}],检查对象[${host_name}],领域[操作系统],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}50%],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
cluster_home_dir_inode_usage_check

# 数据库表空间数量
function mysql_tablesapce_nums()
{
check_entry="数据库表空间数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_tablesapce_nums|grep -qi on;then
	tablespace_nums=`${mylogin_local} -sse "${tablespace_nums_sql}"`
	os_limit=`ulimit -n`
	os_limit_alarm=`echo ${os_limit}|awk '{printf ("%.0f",$1*0.8)}'`
	if [ ${tablespace_nums} -lt ${os_limit_alarm%%.*} ];then
		resurlt="正常"
		check_values=0
		compare_symbol="小于"
	else
		result="异常"
		check_values=1
		compare_symbol="大于"
	fi
		printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${tablespace_nums}个 小于${os_limit_alarm%%.*}个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}${os_limit_alarm%%.*}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
	echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_tablesapce_nums

# 数据库服务和状态检查
### 数据库半同步复制状态 ###
function mysql_repl_check()
{
check_entry="数据库半同步复制状态"
if cat ${conf_file}|grep -v "^#"|grep mysql_repl_check|grep -qi on;then
    if [ "${db_role}" = "master" ];then
            slave_host_num=`${mylogin_local} -sse "${slave_host_num_sql}"|wc -l`
            if [ "${slave_host_num}" -gt 0 ];then
                    result="正常"
                    status=0
		comment="数据库半同步复制服务正常"
            else
                    result="异常"
                    status=1
                    echo "`eval ${date_time}` 数据库半同步复制服务中断" >>${checkout_file}
		comment="数据库半同步复制服务中断"
            fi
    elif [ ${db_role} = "slave" ];then
            slave_status_num=`${mylogin_local} -sse "${slave_status_sql}"|grep -cwi "yes"`
            if [ "${slave_status_num}" == 2 ];then
                    result="正常"
                    status=0
		comment="数据库半同步复制服务正常"
            else
                    result="异常"
                    status=1
                    echo "`eval ${date_time}` 数据库半同步复制服务中断" >>${checkout_file}
		comment="数据库半同步复制服务中断"
            fi
    fi
    printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${status} 0 |tee -a ${output_file}
	check_values=${status}
	echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${comment}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_repl_check

### 数据库半同步复制延迟 ###
function mysql_repl_time_check()
{
check_entry="数据库半同步复制延迟"
if cat ${conf_file}|grep -v "^#"|grep mysql_repl_time_check|grep -qi on;then
        if [ ${db_role} = "slave" ];then
            repl_timeout=`${mylogin_local} -sse "${slave_status_sql}"|grep "Seconds_Behind_Master:"|awk '{print $2}'`
			if [ ${repl_timeout} -eq 0 ] 2>/dev/null;then
                result="正常"
				check_values=0
			else
                result="异常"
				check_values=1
			fi
			echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${repl_timeout}秒],风险级别[],检查时间[`date +%s`]" >>doap.txt
        else
			result="主节点不适用"
            repl_timeout="N/A"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${repl_timeout}秒 0秒 |tee -a ${output_file}
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_repl_time_check

### 数据库GTID检查 ###
function mysql_gtid_check()
{
check_entry="从库已重放的事务数"
if cat ${conf_file}|grep -v "^#"|grep mysql_gtid_check|grep -qi on;then
	if [ ${db_role} = "slave" ];then
            transaction_counter_value=$(${mylogin_local} -sse "${slave_status_sql}"|grep Retrieved_Gtid_Set|awk -F ':' '{print $NF}'|sed 's/-/ /g'|awk '{if (NF==1) print 0;else print $2-$1}')
		if [ ${transaction_counter_value} -lt 1680000000 ];then
			result="正常"
			check_values=0
			compare_symbol="小于"
		else
			result="异常"
			check_values="1"
			compare_symbol="大于"
		fi
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}17亿],风险级别[],检查时间[`date +%s`]" >>doap.txt
	else
		result="主节点不适用"
		transaction_counter_value="N/A"
	fi
	printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${transaction_counter_value}个 小于17亿个 |tee -a ${output_file}
else
	echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_gtid_check

### 数据库Master节点binlog dump线程 ###
function master_binlog_dump_check()
{
check_entry="主节点dump线程数"
if cat ${conf_file}|grep -v "^#"|grep master_binlog_dump_check|grep -qi on;then
        if [ "${db_role}" = "master" ];then
                binlog_dump_num=`${mylogin_local} -sse "${binlog_dump_num_sql}"`
                if [ ${binlog_dump_num} -eq 2 ];then
                        result="正常"
						compare_symbol="等于"
						check_values=0
                else
                        result="异常"
						compare_symbol="小于"
						check_values=1
                fi
				echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}2],风险级别[],检查时间[`date +%s`]" >>doap.txt
        else
                result="从节点不适用"
                binlog_dump_num="N/A"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${binlog_dump_num}个 2个 |tee -a ${output_file}
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
master_binlog_dump_check

### 数据库从节点I/O线程 ###
function slave_io_thread_status_check()
{
check_entry="从节点I/O线程数"
if cat ${conf_file}|grep -v "^#"|grep slave_io_thread_status_check|grep -qi on;then
        if [ "${db_role}" = "slave" ];then
                io_thread_nums=`${mylogin_local} -sse "${slave_status_sql}" 2>/dev/null | grep "Slave_IO_Running:"|grep -ci yes`
                    if [ ${io_thread_nums} = 1 ];then
                            result="正常"
							check_values=0
                        else
                            result="异常"
							check_values=1
                        fi

				echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${result}],风险级别[],检查时间[`date +%s`]" >>doap.txt
        else
                result="主节点不适用"
                io_thread_nums="N/A"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${io_thread_nums}个 1个 |tee -a ${output_file}
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
slave_io_thread_status_check

### 数据库从节点SQL线程 ###
function slave_sql_thread_status_check()
{
check_entry="数据库从节点SQL线程数"
if cat ${conf_file}|grep -v "^#"|grep slave_sql_thread_status_check|grep -qi on;then
        if [ "${db_role}" = "slave" ];then
                sql_thread_nums=`${mylogin_local} -sse "${slave_status_sql}" 2>/dev/null | grep 'Slave_SQL_Running:'| grep -ci yes`
					if [ $sql_thread_nums -eq 1 ];then
                        result="正常"
					    check_values=0
                   else
                        result="异常"
					    check_values=1
                    fi
				echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${result}],风险级别[],检查时间[`date +%s`]" >>doap.txt
        else
                result="主节点不适用"
                sql_thread_nums="N/A"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${sql_thread_nums}个 1个 |tee -a ${output_file}
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
slave_sql_thread_status_check

### 数据库连接使用率 ####
function mysql_conn_usage_check()
{
check_entry="数据库连接使用率"
if cat ${conf_file}|grep -v "^#"|grep mysql_conn_usage_check|grep -qi on;then
        conn_num=`${mylogin_local} -sse "${conn_num_sql}"`
        if [ ${conn_num} -lt 60 ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${conn_num}% 小于60% |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}60],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_conn_usage_check

### 数据库活跃会话数 ###
function mysql_thread_running_check()
{
check_entry="数据库活跃会话数"
if cat ${conf_file}|grep -v "^#"|grep  mysql_thread_running_check|grep -qi on;then

        thread_running_num=`${mylogin_local} -sse "${thread_running_num_sql}"|awk '{print $2}'`
        if [ ${thread_running_num} -lt 60 ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${thread_running_num}个 小于60个|tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}60],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_thread_running_check

### 长事务检查 ###
function mysql_long_transaction_check()
{
check_entry="长事务数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_long_transaction_check|grep -qi on;then
        long_transaction_num=`${mylogin_local} -sse "${long_transaction_num_sql}"`
        if [ ${long_transaction_num} -eq 0 ];then
                result="正常"
				check_values=0
        else
                result="异常"
				check_values=1
				echo "长事务如下" >>${checkout_file}
				${mylogin_local} -sse "${long_transaction_sql}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${long_transaction_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${long_transaction_num}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_long_transaction_check

### 统计访问锁信息 ###
function mysql_access_lock_info_check()
{
check_entry="访问锁信息数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_access_lock_info_check|grep -qi on;then
        access_lock_info_num=`${mylogin} -sse "${access_lock_info_num_sql}"`
        if [ ${access_lock_info_num} -eq 0 ];then
                result="正常"
				check_values=0
        else
                result="异常"
				check_values=1
				echo "访问锁信息如下" >>${checkout_file}
				${mylogin} -e "${access_lock_info_sql}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${access_lock_info_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${access_lock_info_num}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_access_lock_info_check

### 统计执行异常的语句 ###
function mysql_exec_error_check()
{
check_entry="SQL语句执行异常数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_exec_error_check|grep -qi on;then
        exec_error_num=`${mylogin} -sse "${exec_error_num_sql}"`
        if [ ${exec_error_num} -eq 0 ];then
                result="正常"
				check_values=0
        else
                result="异常"
				check_values=1
				echo "执行异常的SQL语句如下" >>${checkout_file}
				${mylogin} -e "${exec_error_sql}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${exec_error_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${exec_error_num}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_exec_error_check


### 统计全表扫描信息 ###
function mysql_full_table_scan_check()
{
check_entry="全表扫描的数量"
if cat ${conf_file}|grep -v "^#"|grep mysql_full_table_scan_check|grep -qi on;then
        full_table_scan_num=`${mylogin} -sse "${full_table_scan_num_sql}"`
        if [ ${full_table_scan_num} -eq 0 ];then
                result="正常"
				check_values=0
        else
                result="异常"
				check_values=1
				echo "全表扫描的SQL语句如下" >>${checkout_file}
				${mylogin} -e "${full_table_scan_sql}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${full_table_scan_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${full_table_scan_num}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_full_table_scan_check


### 统计死锁信息 ###
function msyql_dead_lock_check()
{
check_entry="死锁信息"
if cat ${conf_file}|grep -v "^#"|grep msyql_dead_lock_check|grep -qi on;then
        dead_lock_num=`${mylogin} -sse "${dead_lock_sql}"|grep -i "latest detected deadlock"|wc -l`
        if [ ${dead_lock_num} -eq 0 ];then
                result="正常"
				check_values=0
        else
                result="异常"
				check_values=1
				echo "死锁信息如下" >>${checkout_file}
				${mylogin} -e "${dead_lock_sql}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${dead_lock_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${dead_lock_num}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
msyql_dead_lock_check


# 集群服务检查
### 集群拓扑状态 ###
function cluster_topol_status_check()
{
check_entry="集群数据节点数量"
if cat ${conf_file}|grep -v "^#"|grep cluster_topol_status_check|grep -qi on;then
        topol_status_nums=`${mylogin} -sse "${topol_status_sql}"|grep -cw normal`
        if [ ${topol_status_nums} -ge 3 ];then
                result="正常"
				check_values=0
				compare_symbol="大于或等于"

        else
                result="异常"
				check_values=1
				compare_symbol="小于"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${topol_status_nums}个 大于或等于3个|tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}3],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
cluster_topol_status_check

# 数据库对象检查
### 非innodb引擎表 ###
function not_innodb_engine_table()
{
check_entry="非InnoDB表数量"
if cat ${conf_file}|grep -v "^#"|grep not_innodb_engine_table|grep -qi on;then
        not_innodb_engine_table_num=`${mylogin_local} -sse "${no_innodb_engine_table_sql}"|sed '/^$/d'|wc -l`
        if [ ${not_innodb_engine_table_num} -eq 0 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
                echo "`eval ${date_time}` 非存储引擎表" >> ${checkout_file}
                echo "${not_innodb_engine_table}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${not_innodb_engine_table_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}0],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
not_innodb_engine_table

### 无主键的InnoDB表 ###
function no_primary_innodb_table_check()
{
check_entry="无主键的InnoDB表数量"
if cat ${conf_file}|grep -v "^#"|grep no_primary_innodb_table_check|grep -qi on;then
		no_primary_innodb_table=`${mylogin_local} -sse "${no_primary_innodb_table_sql}"`
        no_primary_innodb_table_num=`echo ${no_primary_innodb_table}|sed '/^$/d'|wc -l`
        if [ ${no_primary_innodb_table_num} -eq 0 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
                echo "`eval ${date_time}` 无主键的InnoDB表" >> ${checkout_file}
                echo "${no_primary_innodb_table}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry}  数据库 ${result} ${no_primary_innodb_table_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}0],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >> ${checkout_file}
fi
}
no_primary_innodb_table_check

### 用户锁定数量检查 ####
function mysql_user_lock_check()
{
check_entry="锁定的用户数量"
if cat ${conf_file}|grep -v "^#"|grep  mysql_user_lock_check|grep -qi on;then
        user_lock_num=`${mylogin_local} -sse "${user_lock_sql}"|sed '/^$/d'|wc -l`
        if [ ${user_lock_num} -eq 0 ];then
                result="正常"
				check_values=0
				compare_symbol="等于"
        else
                result="异常"
				check_values=1
				compare_symbol="大于"
                echo "`eval ${date_time}` 用户锁定" >> ${checkout_file}
                echo "${user_lock}" >> ${checkout_file}
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${user_lock_num}个 0个 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}0],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_user_lock_check

# 日志检查
### 日志切分 ###
system_name=`echo ${host_name:4:7}`
date_format=`date +'%Y%m%d'`
[ ! -z ${system_name} ] || { system_name="mysql"; }

# MySQL 日志
error_log_sql="show variables like 'log_error';"
mysqld_log_sql="show variables like 'general_log_file';"
slow_log_sql="show variables like 'slow_query_log_file';"

error_log=`${mylogin_local} -sse "${error_log_sql}"|awk '{print $2}'`
mysqld_log=`${mylogin_local} -sse "${mysqld_log_sql}"|awk '{print $2}'`
slow_log=`${mylogin_local} -sse "${slow_log_sql}"|awk '{print $2}'`

log_dir=`dirname ${error_log}`
backup_log_dir="${log_dir}/history"

# DBScale 日志
dbscale_log_sql="dbscale show options like 'log-file';"
dbscale_log=`${mylogin} -sse "${dbscale_log_sql}"|awk '{print $2}'`
dbscale_log_dir=`dirname ${dbscale_log}`
backup_dbscale_log_dir=${dbscale_log_dir}/history

# 慢日志大小
function mysql_slowlog_size_check()
{
check_entry="MySQL慢查询日志大小"
if cat ${conf_file}|grep -v "^#"|grep mysql_slowlog_size_check|grep -qi on;then
        mysql_slowlog_size=`du -sm ${slow_log}|awk '{print $1}'`
        if [ ${mysql_slowlog_size} -lt 30 ];then
                result="正常"
				check_values=0
				compare_symbol="小于"
        else
                result='异常'
				check_values=1
				compare_symbol="大于"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${mysql_slowlog_size}M 小于30M |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}30M],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysql_slowlog_size_check

# 普通日志
function mysqld_general_log_check()
{
check_entry="MySQL普通日志"
if cat ${conf_file}|grep -v "^#"|grep mysqld_general_log_check|grep -qi on;then
        mysqld_log_status=`${mylogin_local} -sse "${mysqld_log_status_sql}"|awk '{print $2}'`
        if [ "${mysqld_log_status}" = "OFF" ];then
                result="正常"
                check_values=0
				comment="未开启"
        else
                result="异常"
                check_values=1
				comment="已开启"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${comment} 未开启 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${comment}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
mysqld_general_log_check

# dbscale debug日志
function dbscale_debug_log_check()
{
check_entry="调度节点dbebug日志"
if cat ${conf_file}|grep -v "^#"|grep dbscale_debug_log_check|grep -qi on;then
        dbscale_log_level=`${mylogin} -sse "${dbscale_log_level_sql}"|awk '{print $2}'`
        if [ "${dbscale_log_level}" = "INFO" ];then
                result="正常"
				check_values=0
				comment="未开启"
        else
                result="异常"
				check_values=1
				comment="已开启"
        fi
        printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${comment} 未开启 |tee -a ${output_file}
		echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${comment}],风险级别[],检查时间[`date +%s`]" >>doap.txt
else
        echo "`eval ${date_time}` ${check_entry}，跳过检查" >>${checkout_file}
fi
}
dbscale_debug_log_check

# rds备份计划
# 新增备份节点组判断
function rds_backup_schedule_check()
{
check_entry="备份计划数量"
if cat ${conf_file}|grep -v "^#"|grep rds_backup_schedule_check|grep -qi on;then
rds_conf_file="/data/dbinit/etc/greatrds.conf"
        if ps -ef|grep -qw ${rds_conf_file};then
                # 需配置访问rds元数据库的连接
                db_ip=$(cat ${rds_conf_file} |grep "transport_url"|xargs -d',' -n1|sed '$d'|awk -F'@' '{print $NF}'|sed 's/:.*//g'|head -n1)
                local_ip=$(cat ${rds_conf_file}|grep -w "host"|awk '{print $NF}')
				# 数据库备份信息
				no_backup_schedule_num_sql="select count(distinct b.address) from greatrds.cluster a,greatrds.cluster_member b,greatrds.nodegroup c,greatrds.backup_schedule d where b.nodegroup_id=c.uuid and a.uuid=c.cluster_id and a.uuid=d.cluster_id and d.backup_mode='PHYSICAL_MODE' and b.address='${local_ip}';"

				no_backup_schedule_info_sql="select c.name '集群名称',c.address "IP地址" from greatrds.cluster c where c.uuid not in (select distinct cluster_id from greatrds.backup_schedule s) and c.address='${local_ip}';"
                my_conn="mysql -uroot -pdrACgwoqtM -h${db_ip} -P13336 --ssl-mode=disable"
                no_backup_schedule_num=`${my_conn} -sse "${no_backup_schedule_num_sql}" 2>/dev/null`
                if [ ${no_backup_schedule_num:-0} -gt 0 ];then
                        result="正常"
						check_values=0
						compare_symbol="大于"
                else
                        result="异常"
						check_values=1
						compare_symbol="等于"
                        no_backup_task_info=`${my_conn} -sse "${no_backup_schedule_info_sql}" 2>/dev/null`
                        no_backup_schedule_num=0
                        echo "`eval ${date_time}` 管控平台没有配置备份计划的集群" >> ${checkout_file}
                        echo "${no_backup_task_info}" >> ${checkout_file}
                fi
                printf_cmd 数据库检查 ${check_entry} 数据库 ${result} ${no_backup_schedule_num}个 大于等于1个 |tee -a ${output_file}
				echo "检查项[${check_entry}],检查对象[${host_name}],领域[数据库],实例[],结果类型[1],检查结果[${check_values}],当前值[],结果描述[${check_entry}${compare_symbol}0],风险级别[],检查时间[`date +%s`]" >>doap.txt
        else
           echo "`eval ${date_time}` 该节点未加入管控，跳过检查" >>${checkout_file}
        fi
else
        echo "`eval ${date_time}` 管控平台是否配置集群备份计划检查项关闭，跳过检查" >>${output_file}
fi
}
rds_backup_schedule_check

##==============================================================================================
#上传检查结果到xx.xx.xx.xx服务器
##===============================================================================================

# MySQL 日志归档
[ -d ${backup_log_dir} ] || { mkdir -p ${backup_log_dir}; }
[ -s ${error_log} ] && { cat ${error_log} >> ${backup_log_dir}/${host_name}_${system_name}_mysql_error_${date_format}.log;echo > ${error_log}; }
#[ -s ${mysqld_log} ] && { cat ${mysqld_log} >> ${backup_log_dir}/${host_name}_${system_name}_mysql_mysqld_${date_format}.log;echo > ${mysqld_log}; }
[ -s ${slow_log} ] && { cat ${slow_log} >> ${backup_log_dir}/${host_name}_${system_name}_mysql_slow_${date_format}.log;echo >${slow_log}; }

# DBScale 日志归档
[ -d ${backup_dbscale_log_dir} ] || { mkdir -p ${backup_dbscale_log_dir}; }
[ -s ${dbscale_log} ] && { cat ${dbscale_log} >> ${backup_dbscale_log_dir}/${host_name}_${system_name}_jkmysql_dbscale_${date_format}.log;echo > ${dbscale_log}; }

# 检查结果
echo "`eval ${date_time}` 巡检完成"
[ -s ${checkout_file} ] && { echo "`eval ${date_time}` 请查看巡检日志的异常信息"; }

# 日志归档检查
if [ -f  ${backup_log_dir}/${host_name}_${system_name}_mysql_error_${date_format}.log ];then
        echo "数据库错误日志切分成功"
else
        echo "数据库错误日志切分失败"
fi

if [ -f  ${backup_log_dir}/${host_name}_${system_name}_mysql_slow_${date_format}.log ];then
        echo "数据库慢日志切分成功"
else
        echo "数据库慢日志切分失败"
fi

if [ -f  ${backup_dbscale_log_dir}/${host_name}_${system_name}_jkmysql_dbscale_${date_format}.log ];then
        echo "dbscale日志切分成功"
else
        echo "dbscale日志切分失败"
fi

# 归档历史文件
# find ${tool_dir} -maxdepth 1 -mtime +1 -type f -name '*.log' |xargs -i{} mv {} ${tool_history_dir}

# 输出内容格式适配doap接口
doap_format_content=`cat doap.txt |tr "\n" ";"|sed 's/;$//'|sed 's/;/||/g'`

# 传输检查结果
logplist=("xx.xx.xx.xx" "xx.xx.xx.xx" "xx.xx.xx.xx" "xx.xx.xx.xx" "xx.xx.xx.xx" "xx.xx.xx.xx")
for i in ${logplist[@]}
do
    #echo $i
    ping_result=`ping -c 1 -w 1 $i | grep loss | awk '{print $6}' | awk -F "%" '{print $1}'`
    #echo $ping_result
    if [ $ping_result -eq 100 ];then
        echo "ping $i fail"
    else
        echo "ping $i ok"
    fi
done





```



















