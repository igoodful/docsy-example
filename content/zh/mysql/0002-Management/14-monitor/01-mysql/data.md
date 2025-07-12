---
title: 数据采集
description: 数据采集
date: 2023-10-25
weight: 20000


---

{{<note>}}
<!---->


{{</note>}}



## 一、采集



```sql
show global variables;

show global status;

show full processlist \G
show full processlist;

show slave status\G

select * from performance_schema.threads \G

select * from information_schema.innodb_trx \G

show engine innodb status\G

show engine innodb mutex;

-- mysql8.0
select * from performance_schema.data_locks\G
select * from performance_schema.data_lock_waits\G

-- mysql5.7
select * from information_schema.innodb_lock_waits \G
select * from information_schema.innodb_locks\G

pstack $mpid > mysqld_stack_`date +%Y%m%d_%H%M%S`.txt

perf top -p $mpid

perf record -a -g -F 1000 -p $mpid -o pdata_1.dat
perf report -i pdata_1.dat

SELECT SLEEP(60);

```












## 二、数据采集

```sql
su - mysql
currdt=`date +%Y%m%d_%H%M%S`
echo "$currdt" > /tmp/currdt_tmp.txt
mkdir /tmp/diag_info_`hostname -i`_$currdt
cd /tmp/diag_info_`hostname -i`_$currdt

-- 1. mysql进程负载
su -
cd /tmp/diag_info_`hostname -i`_`cat /tmp/currdt_tmp.txt`

ps -ef | grep -w mysqld
mpid=`pidof mysqld`
echo $mpid

-- b: 批量模式; n: 制定采集测试; d: 间隔时间; H: 线程模式; p: 指定进程号
echo $mpid
top -b -n 120 -d 1 -H -p $mpid > mysqld_top_`date +%Y%m%d_%H%M%S`.txt

-- 2. 信息采集步骤--- 以下窗口，建议启动额外的窗口执行
mysql -uroot -h127.1 -p

tee var-1.txt
show global variables;

tee stat-1.txt
show global status;

tee proclist-1.txt
show full processlist\G
show full processlist;

tee slave_stat-1.txt
show slave status\G

tee threads-1.txt
select * from performance_schema.threads \G

tee innodb_trx-1.txt
select * from information_schema.innodb_trx \G

tee innodb_stat-1.txt
show engine innodb status\G

tee innodb_mutex-1.txt
SHOW ENGINE INNODB MUTEX;

-- 锁与等待信息
tee data_locks-1.txt

-- mysql8.0
select * from performance_schema.data_locks\G
select * from performance_schema.data_lock_waits\G

-- mysql5.7
select * from information_schema.innodb_lock_waits \G
select * from information_schema.innodb_locks\G


-- 3. 堆栈信息
su -
cd /tmp/diag_info_`hostname -i`_`cat /tmp/currdt_tmp.txt`

ps -ef | grep -w mysqld
mpid=`pidof mysqld`
echo $mpid

-- 堆栈信息
echo $mpid
pstack $mpid > mysqld_stack_`date +%Y%m%d_%H%M%S`.txt

-- 线程压力
echo $mpid

perf top
echo $mpid
perf top -p $mpid

perf record -a -g -F 1000 -p $mpid -o pdata_1.dat
perf report -i pdata_1.dat

-- 4. 等待 30 秒
SELECT SLEEP(60);

-- 5. 信息采集步骤--- 以下窗口，建议启动额外的窗口执行
mysql -uroot -h127.1 -p

tee var-2.txt
show global variables;

tee stat-2.txt
show global status;

tee proclist-2.txt
show full processlist\G
show full processlist;

tee slave_stat-2.txt
show slave status\G

tee threads-2.txt
select * from performance_schema.threads \G

tee innodb_trx-2.txt
select * from information_schema.innodb_trx \G

tee innodb_stat-2.txt
show engine innodb status\G

tee innodb_mutex-2.txt
SHOW ENGINE INNODB MUTEX;

-- 锁与等待信息
tee data_locks-2.txt

-- mysql8.0
select * from performance_schema.data_locks\G
select * from performance_schema.data_lock_waits\G

-- mysql5.7
select * from information_schema.innodb_lock_waits \G
select * from information_schema.innodb_locks\G


-- 6. 堆栈信息
su -
cd /tmp/diag_info_`hostname -i`_`cat /tmp/currdt_tmp.txt`

ps -ef | grep -w mysqld
mpid=`pidof mysqld`
echo $mpid

-- 堆栈信息
echo $mpid
pstack $mpid > mysqld_stack_`date +%Y%m%d_%H%M%S`.txt

-- 线程压力
echo $mpid

perf top
echo $mpid
perf top -p $mpid

perf record -a -g -F 1000 -p $mpid -o pdata_2.dat
perf report -i pdata_2.dat

--- END --

```
















