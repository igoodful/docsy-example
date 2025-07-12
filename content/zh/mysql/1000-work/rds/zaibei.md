---
title: 灾备源码解读
description: 灾备源码解读
date: 2023-10-30
weight: 30000


viz: true
---
{ rank=same; b, c, d }
## 架构





```viz-dot
digraph "cluster" {
        nodesep = 0.5;
        start=2;
        ranksep = 1;
        rankdir=TB;
        compound=true;

        edge [
                fontsize="12";

        ];
        node[
                shape=octagon
                fontsize = "12"
        ];

        subgraph cluster_router {
		label = "router";
                router_master[label="M"];
                router_slave1[label="S1"];
                router_slave2[label="S2"];
	}

        subgraph cluster_zookeeper {
		label = "zookeeper";
                zookeeper_master[label="M"];
                zookeeper_slave1[label="S1"];
                zookeeper_slave2[label="S2"];
	}

        subgraph cluster_db {
		label = "db";
                db_master[label="M"];
                db_slave1[label="S1"];
                db_slave2[label="S2"];

	}

        "zookeeper_master" -> "zookeeper_slave1";
        "zookeeper_master" -> "zookeeper_slave2";

        "router_master" -> "router_slave1";
        "router_master" -> "router_slave2";

        "db_master" -> "db_slave1";
        "db_master" -> "db_slave2";

        "router_slave1" -> db_master [lhead="cluster_db" ltail="cluster_router" dir=both];
        "router_slave2" -> zookeeper_master [lhead="cluster_zookeeper" ltail="cluster_router" dir=both];

}
```

## switchover

```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "my_init" -> "my_init_done";
        "my_init_done" -> "my_umask=0640";
        "my_umask=0640" -> "my_umask_dir=0750";
        "my_umask_dir=0750" -> "UMASK";
        "UMASK" -> "UMASK_DIR";
        "UMASK_DIR" -> "my_thread_global_init()" ;
        "my_thread_global_init()" -> "my_thread_init()" ;
        "my_thread_init()" -> "HOME" ;
        "HOME" -> "home_dir" ;
        "home_dir" -> "MyFileInit()" ;

}
```


## failover


```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "my_init" -> "my_init_done";
        "my_init_done" -> "my_umask=0640";
        "my_umask=0640" -> "my_umask_dir=0750";
        "my_umask_dir=0750" -> "UMASK";
        "UMASK" -> "UMASK_DIR";
        "UMASK_DIR" -> "my_thread_global_init()" ;
        "my_thread_global_init()" -> "my_thread_init()" ;
        "my_thread_init()" -> "HOME" ;
        "HOME" -> "home_dir" ;
        "home_dir" -> "MyFileInit()" ;

}
```




## 可能用到的dbscale语句：

```sql
```sql
dbscale show dataservers\G;
dbscale request cluster info\G;
dbscale show datasource type =replication\G;
dbscale show datasource type=server\G;

dbscale show options like 'transparent-mode';
dbscale show options like 'slave-dbscale-mode';
dbscale show options like 'enable-read-only';
dbscale show options like '%enable-slave-dbscale-server%';
dbscale set global 'slave-dbscale-mode'=0/1;
dbscale set global 'enable-read-only' = 0/1;
dbscale set global 'enable-slave-dbscale-server'=1/0;

show slave status\G;
show master status\G;
stop slave;


dbscale dynamic remove datasource slave_dbscale_source;
dbscale dynamic remove dataserver slave_dbscale_server;
dbscale dynamic add dataserver server_name=slave_dbscale_server,server_host="172.17.135.83",server_port=16310,server_user="admin",server_password="123456",dbscale_server;
dbscale dynamic add server datasource slave_dbscale_source slave_dbscale_server-1-1000-400-800 group_id =207;
dbscale dynamic add slave slave_dbscale_source to normal_0;

dbscale enable dataserver normal_0_0/normal_0_1/normal_0_2/slave_dbscale_server;
dbscale disable dataserver normal_0_0/normal_0_1/normal_0_2/slave_dbscale_server;
```




## Examples

```bash
[mysql@global ~]$ ./giogii -h
giogii version: giogii/1.2
Usage: giogii [-si source socket ] [-ti target socket] [-f verify | start | stop | begin | end | switchover ]
Options:
  -c string
        Compare source and target site information
  -config string
        [ dr_switchover.ini | dr_flashback.ini | dr_failover.ini ] ini config file
  -f string
        [ verify | start | stop | begin | end | switchover ] verify: Pre-switchover check for disaster recovery, start: Perform the preparation phase of the flashback action, stop: Perform the follow-up procedure of the flashback action, begin: Perform the preparation phase of the flashback action, end: Perform the follow-up procedure of the flashback action, switchover: The Dr Switchover process is executed
  -m string
        Lock information checking
  -s string
        [ root:root ] Information about the source user
  -si string
        [ 10.10.1.1:3306 ] Indicates the socket information of the source end
  -t string
        [ root:root ] Information about the target user
  -ti string
        [ 10.10.1.1:3306 ] Indicates the socket information of the target end

```

### 1）主备集群位点数据核对
-s 源端集群信息, -si 源端连接信息, -t 目标端集群信息, -ti 目标端连接信息, 两个集群之间如果无GTID和POS差异则返回0

```bash
./giogii -s 'admin:123456' -si '172.16.76.105:16310' -t 'admin:123456' -ti '172.16.128.13:16310'
```

### 2）参数基线比对
-s 管控平台MySQL信息,固定值不需要改, -si 管控平台连接信息, -t 目标端集群信息, -ti 目标端连接信息, -c 管控平台基准参数模板名称

```bash
./giogii -s 'root:drACgwoqtM' -si '172.17.128.49:13336' -t 'admin:123456' -ti '172.17.128.13:16310' -c base
```

### 3）锁信息监控
-s 需要监控的集群用户信息,格式为 username:password,-si 需要监控的集群连接信息,格式为 ip:port -m m 固定写法

```bash
./giogii -s 'admin:123456' -si '172.17.139.26:16310' -m m
```

### 4）灾备集群flashback 该方法使用的是clone slave节点
-f 闪回动作, start执行闪回动作准备阶段, stop执行闪回动作后续流程, -si 主集群连接信息, -ti 灾备集群连接信息  -config 配置文件, 可放在当前路径 ,执行start和stop之间的时间业务是可以对灾备集群进行写入操作
```bash
./giogii -si '172.17.139.26:16310' -ti '172.17.139.26:16320' -f start -config dr_flashback.ini
./giogii -si '172.17.139.26:16310' -ti '172.17.139.26:16320' -f stop -config dr_flashback.ini
```

dr_flashback.ini
```ini
[source]
host = 172.17.139.26
port = 16310
mysql_user = admin
mysql_password = 123456
sys_user = mysql
sys_password = mysql
sys_port = 22

[target]
host = 172.17.139.26
port = 16320
mysql_user = admin
mysql_password = 123456
sys_user = mysql
sys_password = mysql
sys_port = 22
```

### 5）灾备集群flashback使用方法 该方法使用的是proxy flashback工具执行灾备端数据闪回
```Plain-Text
目前支持的语句如下：
1.DDL语句的支持如下：
1.1 CREATE DATABASE： 不支持语句中存在 IF NOT EXISTS，反转的方式是构造 DROP DATABASE 语句，唯一的例外是 dbscale 和 dbscale_tmp 库；
1.2 CREATE TABLE： 不支持语句中存在 IF NOT EXISTS，反转的方式是构造 DROP TABLE 语句；
1.3 RENAME TABLE： 将前后的表名调换；
1.4 DROP TABLE：仅支持迁移过程中的临时表名的操作，反转的方式是重构一张重名的表，但表结构本身不关心；
1.5 其它 DDL 语句暂不支持。
2.DML 语句仅支持 row 格式的 binlog event：
2.1 Delete_rows：将其 event 的 type 类型设置为 Insert_rows，其它不变；
2.2 Insert_rows：将其 event 的 type 类型设置为 Delete_rows，其它不变；
2.3 Update_rows：逐步扫描每一个 row-image，每扫描到两个 row-image，将它们进行调换，重构 event。
3.目前不支持，但是以后可以支持的一些简单语句：例如create user,flush logs这种简单语句，可以做成直接跳过。
```

-f 闪回动作, begin执行闪回动作准备阶段, end执行闪回动作后续流程, -si 主集群连接信息, -ti 灾备集群连接信息, -config 配置文件, 可放在当前路径, 执行begin和end直接的时间业务是可以对灾备集群进行写入操作
```bash
./giogii -si '172.17.139.26:16310' -ti '172.17.139.26:16320' -f begin -config dr_flashback.ini
./giogii -si '172.17.139.26:16310' -ti '172.17.139.26:16320' -f end -config dr_flashback.ini
```

example dr_flashback.ini
```ini
[source]
host = 172.17.139.26
port = 16310
mysql_user = admin
mysql_password = 123456
sys_user = mysql
sys_password = mysql
sys_port = 22

[target]
host = 172.17.139.26
port = 16320
mysql_user = admin
mysql_password = 123456
sys_user = mysql
sys_password = mysql
sys_port = 22
```



### 6) switchover
前端无参数传递可以直接把参数配置到配置文件里
```bash
./giogii -f switchover -config dr_switchover.ini
```
前端有参数传递可使用如下格式
```bash
./giogii -f switchover -si '172.17.139.26:16320' -ti "172.17.139.26:16310" -config dr_switchover.ini
```
example dr_switchover.ini
```ini
[source]
rpl_vip = 172.17.139.26
rpl_port = 16310
rpl_user = admin
rpl_password = 123456

[target]
rpl_vip = 172.17.139.26
rpl_port = 16320
rpl_user = admin
rpl_password = 123456
```


## Contribute
x86
```bash
cd giogii/
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build
```
arm
```bash
cd giogii/
CGO_ENABLED=0 GOOS=linux GOARCH=arm64 GOARM=7 go build
```




## note
Change in giogii v1.0.3 (2024-03-20,General Availability)
switchover notes
1. 灾备集群延迟超过60s不允许切换, Fang Bin
2. 回切后，检查主备复制关系是否正常, Fang Bin
3. 增加对透传模式的支持

flashback notes
1. 增加对透传模式的支持
2. 增加连接数据节点使用adm agent的支持, 适用于不提供ssh免密登录的场景(内测中 预留接口 需要适配)
3. 透传模式集群只能使用clone模式, 禁止使用begin和end

General log notes
1. 优化输出日志的格式
2. 增加流程日志输出的步骤 方便使用者查阅

http listen notes
1. 增加监听http协议端口 (内测中 预留接口 需要适配)
2. 客户端可使用http协议发起post请求传递json格式数据执行灾备切换、闪回 (内测中 预留接口 需要适配)

- url: http://xx.xx.xx.xx:6000/dr/switchover
json:
```json
[
    {
        "Role":"source",
        "Host":"172.17.137.62",
        "MySQL_Port":16310,
        "MySQL_User":"admin",
        "MySQL_Password":"123456"

    },
    {
        "Role":"target",
        "Host":"172.17.137.65",
        "MySQL_Port":16310,
        "MySQL_User":"admin",
        "MySQL_Password":"123456"
    }
]
```

- url: http://xx.xx.xx.xx:6000/dr/flashback/start

json:
```json
[
    {
        "Role":"source",
        "Host":"172.17.137.62",
        "MySQL_Port":16310,
        "MySQL_User":"admin",
        "MySQL_Password":"123456",
        "Sys_User":"mysql",
        "Sys_Password":"mysql",
        "Sys_Port":22,
        "rpc_port":19999
    },
    {
        "Role":"target",
        "Host":"172.17.137.65",
        "MySQL_Port":16310,
        "MySQL_User":"admin",
        "MySQL_Password":"123456",
        "Sys_User":"mysql",
        "Sys_Password":"mysql",
        "Sys_Port":22,
        "rpc_port":19999
    }
]
```

目前支持以下接口:
- url: http://xx.xx.xx.xx:6000/dr/flashback/verify
- url: http://xx.xx.xx.xx:6000/dr/flashback/start
- url: http://xx.xx.xx.xx:6000/dr/flashback/stop
- url: http://xx.xx.xx.xx:6000/dr/flashback/begin
- url: http://xx.xx.xx.xx:6000/dr/flashback/end
- url: http://xx.xx.xx.xx:6000/dr/switchover















## 一、verify
{{<caution>}}

成功标志：check os storage space successfully
{{</caution>}}
### 解析配置文件：

1.  source
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port
2.  target
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port



```go
// 参数config不存在时，则默认参数配置文件为main函数所在目录下的dr_flashback.ini文件
// 解析参数时，特殊字符将会被忽略，比如#$，因此解析可能有错。因此尽量不要用特殊字符

// parse flashback config ...
func parse_flashback_config(conf_file string) error {
	iniConfig, err := ini.Load(conf_file)
	if err != nil {
		return err
	}
	for _, sectionName := range iniConfig.SectionStrings() {
		if strings.Contains(sectionName, "source") {
			s := iniConfig.Section(sectionName)
			global_source_instance.MySQL_Ip = s.Key("host").String()
			global_source_instance.MySQL_Port = s.Key("port").String()
			global_source_instance.MySQL_User = s.Key("mysql_user").String()
			global_source_instance.MySQL_Password = s.Key("mysql_password").String()
			global_source_instance.SysUser = s.Key("sys_user").String()
			global_source_instance.SysPassword = s.Key("sys_password").String()
			global_source_instance.SysPort = s.Key("sys_port").String()
		}
		if strings.Contains(sectionName, "target") {
			s := iniConfig.Section(sectionName)
			global_target_instance.MySQL_Ip = s.Key("host").String()
			global_target_instance.MySQL_Port = s.Key("port").String()
			global_target_instance.MySQL_User = s.Key("mysql_user").String()
			global_target_instance.MySQL_Password = s.Key("mysql_password").String()
			global_target_instance.SysUser = s.Key("sys_user").String()
			global_target_instance.SysPassword = s.Key("sys_password").String()
			global_target_instance.SysPort = s.Key("sys_port").String()
		}
	}
	flashback_log.Info("parse flashback config successfully")
	return nil
}


```



```go
// sourceSocket 为-si后面的参数
// targetSocket 为-ti后面的参数
// global_source_instance 为全局生产集群router节点，其中ip和router的端口号被命令行的-si参数覆盖
// global_target_instance 为全局灾备集群router节点，其中ip和router的端口号被命令行的-ti参数覆盖
//

func Do_prefix_check(sourceSocket string, targetSocket string, config string) error {

	var source_nodes = make([]Node, 0)
	var target_nodes = make([]Node, 0)

	err := parse_flashback_config(config)
	if err != nil {
		return err
	}

	global_source_instance.MySQL_Ip = strings.Split(sourceSocket, ":")[0]
	global_source_instance.MySQL_Port = strings.Split(sourceSocket, ":")[1]
	global_source_instance.Database = "information_schema"

	global_target_instance.MySQL_Ip = strings.Split(targetSocket, ":")[0]
	global_target_instance.MySQL_Port = strings.Split(targetSocket, ":")[1]
	global_target_instance.Database = "information_schema"

	err = global_source_instance.init_mysql_conn()
	if err != nil {
		return err
	}
	err = global_target_instance.init_mysql_conn()
	if err != nil {
		return err
	}

	var source_data_servers []dataServers
	err = global_source_instance.get_multi_row_data(&source_data_servers, SHOW_DATASERVERS)
	if err != nil {
		return err
	}

	for _, dataserver := range source_data_servers {
		if dataserver.Servername == "slave_dbscale_server" {
			continue
		}
		var ins Node
		ins.MySQL_Ip = dataserver.Host
		ins.MySQL_Port = dataserver.Port
		ins.MySQL_User = global_source_instance.MySQL_User
		ins.MySQL_Password = global_source_instance.MySQL_Password
		ins.Database = global_source_instance.Database

		ins.SysIp = ins.MySQL_Ip
		ins.SysPort = global_source_instance.SysPort
		ins.SysUser = global_source_instance.SysUser
		ins.SysPassword = global_source_instance.SysPassword

		ins.ServerName = dataserver.Servername

		err = ins.init_mysql_conn()
		if err != nil {
			return err
		}

		err = ins.init_sys_con()
		if err != nil {
			return err
		}

		source_nodes = append(source_nodes, ins)
	}

	// target data servers check
	var target_data_servers []dataServers
	err = global_target_instance.get_multi_row_data(&target_data_servers, SHOW_DATASERVERS)
	if err != nil {
		return err
	}

	for _, dataserver := range target_data_servers {
		if dataserver.Servername == "slave_dbscale_server" {
			continue
		}
		var ins Node
		ins.MySQL_Ip = dataserver.Host
		ins.MySQL_Port = dataserver.Port
		ins.MySQL_User = global_target_instance.MySQL_User
		ins.MySQL_Password = global_target_instance.MySQL_Password
		ins.Database = global_target_instance.Database

		ins.SysIp = ins.MySQL_Ip
		ins.SysPort = global_target_instance.SysPort
		ins.SysUser = global_target_instance.SysUser
		ins.SysPassword = global_target_instance.SysPassword

		ins.ServerName = dataserver.Servername

		err = ins.init_mysql_conn()
		if err != nil {
			return err
		}

		err = ins.init_sys_con()
		if err != nil {
			return err
		}

		target_nodes = append(target_nodes, ins)
	}

	err = check_dataserver_status(global_source_instance)
	if err != nil {
		return err
	}

	err = check_dataserver_status(global_target_instance)
	if err != nil {
		return err
	}

	err = check_secondary_cluster_exists(global_source_instance, global_target_instance)
	if err != nil {
		return err
	}

	err = check_primary_contains_secondary_gtidset(global_source_instance, global_target_instance)
	if err != nil {
		return err
	}

	err = check_os_storage_space(source_nodes)
	if err != nil {
		return err
	}

	err = check_os_storage_space(target_nodes)
	if err != nil {
		return err
	}

	return nil
}



```



### si和ti节点创建数据库连接和ping

```go
// init mysql connection with pass ...
// 根据节点的mysql连接信息，创建连接，然后ping
// 正常：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}
// 异常：mysql create connection failed 和 mysql ping connection failed

func (d *Node) init_mysql_conn() error {
	dbSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8", d.MySQL_User, d.MySQL_Password, d.MySQL_Ip, d.MySQL_Port, d.Database)
	db, err := sqlx.Open("mysql", dbSource)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql create connection failed")
		return err
	}

	if err = db.Ping(); err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql ping connection failed")
		return err
	}
	flashback_log.Info("mysql ping connection successfully")

	db.SetConnMaxIdleTime(time.Minute * 5)
	db.SetMaxOpenConns(1)
	d.db = db
	return nil
}

// si节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}
// ti节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:58:18"}
```



### si节点执行：dbscale show dataservers

```go
// get_multi_row_data
// router节点执行：dbscale show dataservers
// 然后返回结果


func (d *Node) get_multi_row_data(destSlice interface{}, sqlStr string) error {
	if d.db == nil {
		d.init_mysql_conn()
	}
	err := d.db.Select(destSlice, sqlStr)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"username":  d.MySQL_User,
			"password":  d.MySQL_Password,
			"ip":        d.MySQL_Ip,
			"port":      d.MySQL_Port,
			"database":  d.Database,
			"statement": sqlStr,
			"errMsg":    err.Error(),
		}).Error("query failed")
		return err
	}
	flashback_log.WithFields(logrus.Fields{
		"username":  d.MySQL_User,
		"password":  d.MySQL_Password,
		"ip":        d.MySQL_Ip,
		"port":      d.MySQL_Port,
		"database":  d.Database,
		"statement": sqlStr,
	}).Debug("query successfully")
	return nil
}

// 正常：{"database":"information_schema","ip":"172.17.138.0","level":"debug","msg":"query successfully","password":"123456","port":"3307","statement":"dbscale show dataservers","time":"2023-09-19 23:02:55","username":"admin"}
```



### 生产和灾备每个节点测试数据库ping和ssh连接

```go
// 根据si节点执行的dbscale show dataservers获取生产集群的所有节点
// 筛选规则是：servername不为slave_dbscale_server
// 除了host和port信息从dbscale show dataservers获取，其余信息从全局节点global_source_instance获取
// 生产集群每个节点都

for _, dataserver := range source_data_servers {
		if dataserver.Servername == "slave_dbscale_server" {
			continue
		}
		var ins Node
		ins.MySQL_Ip = dataserver.Host
		ins.MySQL_Port = dataserver.Port
		ins.MySQL_User = global_source_instance.MySQL_User
		ins.MySQL_Password = global_source_instance.MySQL_Password
		ins.Database = global_source_instance.Database

		ins.SysIp = ins.MySQL_Ip
		ins.SysPort = global_source_instance.SysPort
		ins.SysUser = global_source_instance.SysUser
		ins.SysPassword = global_source_instance.SysPassword

		ins.ServerName = dataserver.Servername

		err = ins.init_mysql_conn()
		if err != nil {
			return err
		}

		err = ins.init_sys_con()
		if err != nil {
			return err
		}

		source_nodes = append(source_nodes, ins)
	}

// 正常：
// {"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 23:14:06"}
// {"level":"info","msg":"init sys con by pass failed ","time":"2023-09-19 23:14:09"}
// {"level":"info","msg":"init sys con by rsa successfully","time":"2023-09-19 23:14:10"}

```



#### （1）节点创建数据库连接和ping





#### （2）节点ssh密码和密钥连接

```go

// 由于读取ini文件有问题，比如#$这些特殊字符无法读取，导致读取的系统用户的密码是错误的
// 因此，当包含特殊字符时，一定会有报错：{"level":"info","msg":"init sys con by pass failed ","time":"2023-05-18 23:16:27"}


// 优先通过密码方式登录
// 再通过ssh密钥登录
// 若两种都失败才会中止程序
func (c *Node) init_sys_con() error {
	config := &ssh.ClientConfig{}
	config.SetDefaults()
	config.User = c.SysUser
	config.Auth = []ssh.AuthMethod{ssh.Password(c.SysPassword)}
	config.HostKeyCallback = func(hostname string, remote net.Addr, key ssh.PublicKey) error {
		return nil
	}
	client, err := ssh.Dial("tcp", fmt.Sprintf("%s:%s", c.SysIp, c.SysPort), config)
	if nil != err {
		flashback_log.Info("init sys con by pass failed ")
		err = c.init_sys_con_by_rsa()
		if err != nil {
			flashback_log.WithFields(logrus.Fields{
				"username": c.SysUser,
				"ip":       c.SysIp,
				"port":     c.SysPort,
				"errMsg":   err.Error(),
			}).Error("init sys con by rsa failed ")
			return err
		}
		flashback_log.Info("init sys con by rsa successfully")
		return nil
	}
	c.Client = client
	flashback_log.Info("init sys con by pass successfully")
	return nil
}

// {"level":"info","msg":"init sys con by rsa successfully","time":"2023-09-19 20:27:55"}
// {"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 20:27:56"}

```





### 生产和灾备router检查：dbscale show dataservers中slave_dbscale_server

判断规则：Servername包含server

slave_dbscale_server节点的状态status若不为空且包含down，则报错

```go
// err = check_dataserver_status(global_source_instance)
// err = check_dataserver_status(global_target_instance)
// check dataserver status
// 作用：通过生产库任意一个router节点来获取数据节点信息：即执行dbscale show dataservers;
//  servername: slave_dbscale_server，通过servername识别出连接生产集群的灾备节点
// 灾备节点状态Status字段若不为空且包含down，则报错中止
func check_dataserver_status(sourceIns Node) error {
	var dataServer []dataServers
	strSql := "dbscale show dataservers"
	err := sourceIns.get_multi_row_data(&dataServer, strSql)
	if err != nil {
		return err
	}
	for _, server := range dataServer {
		if strings.Contains(server.Servername, "server") { // contains backup cluster server
			if server.Status != "" && strings.Contains(server.Status, "down") {
				err = errors.New("server down")
				flashback_log.Error(err)
				return err
			}
		}
	}
	return nil
}

// dbscale show dataservers;

```



#### dbscale show dataservers;

```sql
-- 生产集群
mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: database_1
                          host: 172.17.138.1
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: database_2
                          host: 172.17.138.0
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: database_3
                          host: 172.17.138.2
                          port: 3308
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 4. row ***************************
                    servername: slave_dbscale_server
                          host: 172.17.138.181
                          port: 3307
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user:
                   remote_port:
max_needed_conn/max_mysql_conn:
               master_priority: 0
4 rows in set (0.00 sec)

-- 灾备集群

mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: database_1
                          host: 172.17.138.182
                          port: 3308
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: database_2
                          host: 172.17.138.180
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: database_3
                          host: 172.17.138.181
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
3 rows in set (0.00 sec)

```



### 生产集群router执行：dbscale show dataservers中slave_dbscale_server必须存在

global_source_instance

```go

// check replication relationship
// 通过生产集群router节点执行：dbscale show dataservers
// 通过判断数据节点的servername字段是否包含名称为slave_dbscale_server的数据节点，若不包含，则直接报错中止
// 正常：{"level":"info","msg":"check secondary cluster exists successfully","time":"2023-09-19 21:23:19"}
func check_secondary_cluster_exists(sourceIns Node, targetIns Node) error {
	var dataServer []dataServers
	strSql := "dbscale show dataservers"
	err := sourceIns.get_multi_row_data(&dataServer, strSql)
	if err != nil {
		return err
	}
	for _, ds := range dataServer {
		if ds.Servername == "slave_dbscale_server" {
			flashback_log.Info("check secondary cluster exists successfully")
			return nil
		}
	}
	err = errors.New("check secondary cluster exists failed")
	flashback_log.Error(err)
	return err
}


```



### 粗略生产集群gtid是否包含了灾备集群的gtid

```go
// 只能粗略检测
// 先通过分别在生产和灾备集群主库执行：show master status
// 通过将灾备集群的gtid用逗号分割分离出来后，拿到生产集群中去比对，确定是否包含了灾备集群gtid的每个部分
// 问题：这么做的话，必须确保主从没有延迟，否则，可能会误报错误，此时需要手工确认。
func check_primary_contains_secondary_gtidset(source_instance Node, target_instance Node) error {
	var source_master_status MasterStatus
	var target_master_status MasterStatus
	err := source_instance.get_single_row_data(&source_master_status, SHOW_MASTER_STATUS)
	if err != nil {
		return err
	}

	err = target_instance.get_single_row_data(&target_master_status, SHOW_MASTER_STATUS)
	if err != nil {
		return err
	}
	target_exec_gtid_set := strings.Split(target_master_status.ExecutedGtidSet, ",")
	for _, target_gtid := range target_exec_gtid_set {

		if !strings.Contains(source_master_status.ExecutedGtidSet, strings.ReplaceAll(target_gtid, "\n", "")) {
			err = errors.New(" The primary cluster is missing the GTID of the secondary cluster, ****** Please handle manually ****** ")
			flashback_log.WithFields(logrus.Fields{
				"source_master_status": strings.ReplaceAll(source_master_status.ExecutedGtidSet, "\n", ""),
				"target_master_status": strings.ReplaceAll(target_master_status.ExecutedGtidSet, "\n", ""),
				"diff_gtid_set":        target_gtid,
			}).Error(err)
			return err
		}
	}
	flashback_log.Info("check primary contains secondary gtidset successfully")
	return nil
}

// get_single_row_data
// 执行：show master status
// 正确：{"database":"information_schema","ip":"172.17.138.0","level":"debug","msg":"query successfully","password":"123456","port":"3307","statement":"show master status","time":"2023-09-19 21:31:33","username":"admin"}
func (d *Node) get_single_row_data(obj interface{}, sqlStr string) error {
	if d.db == nil {
		d.init_mysql_conn()
	}
	err := d.db.Get(obj, sqlStr)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"username":  d.MySQL_User,
			"password":  d.MySQL_Password,
			"ip":        d.MySQL_Ip,
			"port":      d.MySQL_Port,
			"database":  d.Database,
			"statement": sqlStr,
			"errMsg":    err.Error(),
		}).Error("query failed")
		return err
	}
	flashback_log.WithFields(logrus.Fields{
		"username":  d.MySQL_User,
		"password":  d.MySQL_Password,
		"ip":        d.MySQL_Ip,
		"port":      d.MySQL_Port,
		"database":  d.Database,
		"statement": sqlStr,
	}).Debug("query successfully")
	return nil
}




```



```sql
-- 生产集群
mysql> show master status\G
*************************** 1. row ***************************
             File: binlog.000012
         Position: 7640
     Binlog_Do_DB:
 Binlog_Ignore_DB: dbscale_tmp
Executed_Gtid_Set: b9da174e-5208-11ee-8c26-00163e2d01d5:1-49,
ba09b02c-5208-11ee-b5af-00163ed307b5:1-114499,
ba245d88-5208-11ee-aae7-00163ee05610:1-4,
f9958bc0-5604-11ee-a5a1-00163ea7a91c:1-4,
f9f2f2be-5604-11ee-b389-00163e3cdeea:1-20
1 row in set (0.04 sec)

mysql>


-- 灾备集群
mysql> show master status\G
*************************** 1. row ***************************
             File: binlog.000013
         Position: 356
     Binlog_Do_DB:
 Binlog_Ignore_DB: dbscale_tmp
Executed_Gtid_Set: b9da174e-5208-11ee-8c26-00163e2d01d5:1-49,
ba09b02c-5208-11ee-b5af-00163ed307b5:1-114499,
ba245d88-5208-11ee-aae7-00163ee05610:1-4,
f9958bc0-5604-11ee-a5a1-00163ea7a91c:1-4,
f9f2f2be-5604-11ee-b389-00163e3cdeea:1-20
1 row in set (0.00 sec)

mysql>

```





### 检查所有节点数据节点的数据目录的磁盘空间是否剩余55%以上

```go
// check os storage space
// 阈值是数据目录所在磁盘使用量是否超过45%
// （1）df -h /data/mysqldata/ | awk 'NR>1 {print $(NF-1)}'，在adm中是/data/dbdata，需要修改
// （2）ServerName是否包含normal，在adm中需要去掉

func check_os_storage_space(target_ins []Node) error {
	for _, ins := range target_ins {
		if !strings.Contains(ins.ServerName, "normal") {
			continue
		}
		strCmd := "df -h /data/mysqldata/ | awk 'NR>1 {print $(NF-1)}'"
		s, err := ins.Run(strCmd)
		if err != nil {
			return err
		}
		s = strings.ReplaceAll(s, "%\n", "")
		if used, _ := strconv.Atoi(s); used > 45 {
			err = errors.New("insufficient storage space")
			flashback_log.WithFields(logrus.Fields{
				"current value":  s,
				"data path":      "/data/mysqldata/",
				"required value": "No more than 45 is allowed",
			}).Error(err)
			return err
		}
		flashback_log.WithFields(logrus.Fields{
			"current value":  s,
			"data path":      "/data/mysqldata/",
			"required value": "No more than 45 is allowed",
		}).Info("check os storage space successfully")
	}
	return nil
}


// 正常：
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.1","level":"debug","msg":"exec successfully","port":"22","result":"26%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:10:44"}
{"current value":"26","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:12:33"}
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.0","level":"debug","msg":"exec successfully","port":"22","result":"24%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:12:37"}
{"current value":"24","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:12:59"}
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.2","level":"debug","msg":"exec successfully","port":"22","result":"29%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:13:05"}
{"current value":"29","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:13:12"}
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.182","level":"debug","msg":"exec successfully","port":"22","result":"16%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:13:39"}
{"current value":"16","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:13:50"}
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.180","level":"debug","msg":"exec successfully","port":"22","result":"18%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:13:57"}
{"current value":"18","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:14:04"}
{"cmd":"df -h /data/dbdata/ | awk 'NR\u003e1 {print $(NF-1)}'","ip":"172.17.138.181","level":"debug","msg":"exec successfully","port":"22","result":"17%\n","sshPass":"QWer12","sshUser":"mysql","time":"2023-09-19 22:14:08"}
{"current value":"17","data path":"/data/mysqldata/","level":"info","msg":"check os storage space successfully","required value":"No more than 45 is allowed","time":"2023-09-19 22:14:13"}

```





```bash
[root@gip ~]# df -h
文件系统        容量  已用  可用 已用% 挂载点
devtmpfs        3.5G     0  3.5G    0% /dev
tmpfs           3.8G     0  3.8G    0% /dev/shm
tmpfs           3.8G  522M  3.2G   14% /run
tmpfs           3.8G     0  3.8G    0% /sys/fs/cgroup
/dev/sda4        98G   16G   82G   17% /
tmpfs           3.8G  3.8G     0  100% /tmp
/dev/sda2      1014M  197M  818M   20% /boot
/dev/sda1       200M  5.8M  195M    3% /boot/efi
tmpfs           759M     0  759M    0% /run/user/0
[root@gip ~]# df -h /data/ | awk 'NR>1 {print $(NF-1)}'
17%

```





## 二、start
{{<caution>}}
<!---->
成功标志是：exec start successfully
{{</caution>}}




### 1、解析配置文件：

1.  source
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port
2.  target
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port



```go
// 参数config不存在时，则默认参数配置文件为main函数所在目录下的dr_flashback.ini文件
// 解析参数时，特殊字符将会被忽略，比如#$，因此解析可能有错。因此尽量不要用特殊字符

// parse flashback config ...
func parse_flashback_config(conf_file string) error {
	iniConfig, err := ini.Load(conf_file)
	if err != nil {
		return err
	}
	for _, sectionName := range iniConfig.SectionStrings() {
		if strings.Contains(sectionName, "source") {
			s := iniConfig.Section(sectionName)
			global_source_instance.MySQL_Ip = s.Key("host").String()
			global_source_instance.MySQL_Port = s.Key("port").String()
			global_source_instance.MySQL_User = s.Key("mysql_user").String()
			global_source_instance.MySQL_Password = s.Key("mysql_password").String()
			global_source_instance.SysUser = s.Key("sys_user").String()
			global_source_instance.SysPassword = s.Key("sys_password").String()
			global_source_instance.SysPort = s.Key("sys_port").String()
		}
		if strings.Contains(sectionName, "target") {
			s := iniConfig.Section(sectionName)
			global_target_instance.MySQL_Ip = s.Key("host").String()
			global_target_instance.MySQL_Port = s.Key("port").String()
			global_target_instance.MySQL_User = s.Key("mysql_user").String()
			global_target_instance.MySQL_Password = s.Key("mysql_password").String()
			global_target_instance.SysUser = s.Key("sys_user").String()
			global_target_instance.SysPassword = s.Key("sys_password").String()
			global_target_instance.SysPort = s.Key("sys_port").String()
		}
	}
	flashback_log.Info("parse flashback config successfully")
	return nil
}


```





### 2、si和ti节点创建数据库连接和ping

```go
// global_source_instance的ip和port初始化为命令行参数-si的值

// global_target_instance的ip和port初始化为命令行参数-si的值

// init mysql connection with pass ...
// 根据节点的mysql连接信息，创建连接，然后ping
// 正常：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}
// 异常：mysql create connection failed 和 mysql ping connection failed

func (d *Node) init_mysql_conn() error {
	dbSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8", d.MySQL_User, d.MySQL_Password, d.MySQL_Ip, d.MySQL_Port, d.Database)
	db, err := sqlx.Open("mysql", dbSource)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql create connection failed")
		return err
	}

	if err = db.Ping(); err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql ping connection failed")
		return err
	}
	flashback_log.Info("mysql ping connection successfully")

	db.SetConnMaxIdleTime(time.Minute * 5)
	db.SetMaxOpenConns(1)
	d.db = db
	return nil
}

// si节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}
// ti节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:58:18"}



```





### 3、ti节点执行：dbscale show dataservers

```go
// select dataservers
// target_data_servers 为灾备集群执行：dbscale show dataservers后的结果
// 根据 字段master-online-status是否为Master_Online来确定角色是primary还是joiner
// 每个节点的DonorPort就是数据节点的端口号，JoinerPort固定为18000
// 每个数据节点执行：show variables like 'datadir' 获取数据目录
	var target_data_servers []dataServers
	strSql := "dbscale show dataservers"
	err = global_target_instance.get_multi_row_data(&target_data_servers, strSql)
	if err != nil {
		return err
	}

	for _, dataserver := range target_data_servers {
		var node Node
		var v variables

		node.MySQL_Ip = dataserver.Host
		node.MySQL_Port = dataserver.Port
		node.MySQL_User = global_target_instance.MySQL_User
		node.MySQL_Password = global_target_instance.MySQL_Password
		node.DonorPort = dataserver.Port
		node.JoinerPort = "18000"
		node.Database = global_target_instance.Database
		node.ServerName = dataserver.Servername

		node.SysIp = node.MySQL_Ip
		node.SysPort = global_target_instance.SysPort
		node.SysUser = global_target_instance.SysUser
		node.SysPassword = global_target_instance.SysPassword

		if dataserver.MasterOnlineStatus == "Master_Online" {
			node.Role = "primary"
		} else {
			node.Role = "joiner"
		}

		err = node.init_mysql_conn()
		if err != nil {
			return err
		}

		//strSql = "show variables like 'datadir'"
		err = node.get_single_row_data(&v, SHOW_VAR_LIKE_DATADIR) // /data/mysqldata/16315fff-38f1-43d2-99de-06196c083f22/dbdata/
		if err != nil {
			return err
		}

		tmpArr := strings.Split(v.Value, "/")
		node.DataDir = tmpArr[len(tmpArr)-3]

		target_nodes = append(target_nodes, node)
	}



```


```sql
-- target_data_servers 为灾备集群执行：dbscale show dataservers后的结果


mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: database_1
                          host: 172.17.138.1
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: database_2
                          host: 172.17.138.0
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: database_3
                          host: 172.17.138.2
                          port: 3308
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
3 rows in set (0.01 sec)






```





### 4、克隆准备

```go
	err = do_clone_prepare(global_source_instance, global_target_instance, target_nodes)
	if err != nil {
		add_back_cluster(global_source_instance, global_target_instance)
		disable_and_enable_donor_node(global_target_instance, target_nodes, "enable")
		return err
	}
//-------------------------------------------------------------------------------



func do_clone_prepare(sourceIns Node, targetIns Node, cloneIns []Node) error {
	//1) VerifyBIsASlave
	err := check_secondary_cluster_exists(sourceIns, targetIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 2) check Replication Relationship
	err = check_replication_relationship(sourceIns, targetIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 3) remove backup cluster
	err = remove_secondary_cluster(sourceIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 4) stop replica
	err = stop_replica(targetIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 5) waiting for backup cluster apply binlog
	err = check_backup_cluster_seconds_behind_master(targetIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 6) waiting for backup cluster consistent
	err = waiting_for_secondary_cluster_consistent(cloneIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 7) disable donor node
	err = disable_and_enable_donor_node(targetIns, cloneIns, "disable")
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)

	// 8) set Read write
	err = enable_read_write(targetIns)
	if err != nil {
		return err
	}
	time.Sleep(2 * time.Second)
	return nil
}



```







#### 1、check_secondary_cluster_exists：确保生产节点存在servername为slave_dbscale_server的节点

si节点上执行：dbscale show dataservers，确定slave_dbscale_server存在

```go
// check replication relationship
// 通过在si节点上执行：dbscale show dataservers，获取dataServer数组
// 遍历数据节点，若存在servername字段为slave_dbscale_server，则表明正常

func check_secondary_cluster_exists(sourceIns Node, targetIns Node) error {
	var dataServer []dataServers
	strSql := "dbscale show dataservers"
	err := sourceIns.get_multi_row_data(&dataServer, strSql)
	if err != nil {
		return err
	}
	for _, ds := range dataServer {
		if ds.Servername == "slave_dbscale_server" {
			flashback_log.Info("check secondary cluster exists successfully")
			return nil
		}
	}
	err = errors.New("check secondary cluster exists failed")
	flashback_log.Error(err)
	return err
}

// 正常：
// check secondary cluster exists successfully
// 错误：
// check secondary cluster exists failed

```





#### 2、check_replication_relationship：确保灾备主节点连接生产主节点

-   在ti节点上执行：show slave status，获取Master_Host: 172.17.138.2
-   在si节点上执行：dbscale show dataservers，获取master-online-status: Master_Online
-   通过Master_Host与 Master_Online状态的节点ip相等，就证明主从关系正常。



```go

func check_replication_relationship(sourceIns Node, targetIns Node) error {
	var d []dataServers
	strSql := "dbscale show dataservers"
	err := sourceIns.get_multi_row_data(&d, strSql)
	if err != nil {
		return err
	}

	var s SlaveStatus
	strSql = "show slave status"
	err = targetIns.get_single_row_data(&s, strSql)
	if err != nil {
		return err
	}

	for _, v := range d {
		if strings.Contains(v.MasterOnlineStatus, "Master_Online") && strings.EqualFold(v.Host, s.MasterHost) {
			return nil
		}
	}
	return errors.New("check replication failed")
}
// 失败：check replication failed

```





```sql
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 172.17.138.2
                  Master_User: dbscale_internal
                  Master_Port: 3308
                Connect_Retry: 60
              Master_Log_File: binlog.000012
          Read_Master_Log_Pos: 7640
               Relay_Log_File: relaylog.000004
                Relay_Log_Pos: 489
        Relay_Master_Log_File: binlog.000012
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 7640
              Relay_Log_Space: 1941
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1001503308
                  Master_UUID: ba09b02c-5208-11ee-b5af-00163ed307b5
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: ba09b02c-5208-11ee-b5af-00163ed307b5:114497-114499,
f9f2f2be-5604-11ee-b389-00163e3cdeea:19-20
            Executed_Gtid_Set: b9da174e-5208-11ee-8c26-00163e2d01d5:1-49,
ba09b02c-5208-11ee-b5af-00163ed307b5:1-114499,
ba245d88-5208-11ee-aae7-00163ee05610:1-4,
f9958bc0-5604-11ee-a5a1-00163ea7a91c:1-4,
f9f2f2be-5604-11ee-b389-00163e3cdeea:1-20
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.01 sec)


```



#### 3、在si节点上删除灾备信息

-   dbscale dynamic remove datasource slave_dbscale_source
-   dbscale dynamic remove dataserver slave_dbscale_server

```go

func remove_secondary_cluster(ins Node) error {
	strSql := "dbscale dynamic remove datasource slave_dbscale_source"
	_, err1 := ins.exec_dml_stmt(strSql)

	strSql = "dbscale dynamic remove dataserver slave_dbscale_server"
	_, err2 := ins.exec_dml_stmt(strSql)

	if err1 != nil || err2 != nil {
		return errors.New("failed")
	}
	flashback_log.Info("remove backup cluster successfully")
	return nil
}

// 正确：remove backup cluster successfully
// 错误：failed

```



#### 4、在ti节点上停止复制并设置'slave-dbscale-mode'=0

-   stop slave
-   dbscale set global 'slave-dbscale-mode'=0



```go


func stop_replica(ins Node) error {
	if ins.db == nil {
		return errors.New("connection is nil")
	}

	strSql := "stop slave"
	_, err := ins.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "dbscale set global 'slave-dbscale-mode'=0"
	_, err = ins.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}
	flashback_log.Info("stop replica successfully")
	return nil
}

// 正确：stop replica successfully
// 错误：
```



#### 5、在ti节点上执行：show slave status，当Seconds_Behind_Master: NULL时，表示binlog日志应用完成

```go
func check_backup_cluster_seconds_behind_master(target_ins Node) error {
	if target_ins.db == nil {
		return errors.New("connection is nil")
	}
	for {
		var slaveStatus SlaveStatus
		strSql := "show slave status"
		err := target_ins.get_single_row_data(&slaveStatus, strSql)
		if err != nil {
			return err
		}
		if slaveStatus.SecondsBehindMaster == nil {
			break
		}
		time.Sleep(3 * time.Second)
	}
	flashback_log.Debug("waiting for backup cluster apply binlog successfully")
	return nil
}

// 正确：waiting for backup cluster apply binlog successfully

```





#### 6、从灾备数据节点中确定master节点和与master节点有相同gtid的slave作为donor角色

-   遍历灾备数据节点，获取灾备集群的master节点：Role 为"primary"，执行：show master status
-   遍历灾备数据节点中Role为"joiner"的数据节点，通过执行：show slave status
-   当joiner数据节点的gtid与primary数据节点的gtid相同，则设置joiner数据节点的Role 为"donor"，并退出，否则一直循环

```go


func waiting_for_secondary_cluster_consistent(cloneIns []Node) error {
	var primary *Node
	for {
		for _, ins := range cloneIns {
			if ins.Role == "primary" {
				primary = &ins
				break
			}
		}

		var masterStatus MasterStatus
		strSql := "show master status"
		err := primary.get_single_row_data(&masterStatus, strSql)
		if err != nil {
			return err
		}

		for i, ins := range cloneIns {
			if ins.Role == "joiner" {
				var slaveStatus SlaveStatus
				strSql := "show slave status"
				err := ins.get_single_row_data(&slaveStatus, strSql)
				if err != nil {
					return err
				}

				// get global transaction id
				if masterStatus.ExecutedGtidSet == slaveStatus.ExecutedGtidSet {
					cloneIns[i].Role = "donor"
					flashback_log.Debug("waiting for backup cluster consistent successfully")
					return nil
				}
			}

		}
		time.Sleep(3 * time.Second)
	}
}

// 正确：waiting for backup cluster consistent successfully

```



#### 7、将灾备集群的数据节点中角色为donor的数据节点disable：该节点会断开主从复制

-   在ti路由节点上执行：dbscale show dataservers
-   根据角色为donor的数据节点的host和master-online-status: Not_A_Master来确定：servername
-   找到servername后，在ti路由节点上执行：dbscale disable dataserver servername名称

```go



func disable_and_enable_donor_node(targetIns Node, cloneIns []Node, cmd string) error {
	if targetIns.db == nil {
		return errors.New("connection is nil")
	}

	var dataServer []dataServers

	strSql := "dbscale show dataservers"
	err := targetIns.get_multi_row_data(&dataServer, strSql)
	if err != nil {
		return err
	}

	if len(dataServer) == 0 {
		return errors.New(" dataServer length = 0 ")
	}

	if cmd == "enable" {
		for _, v := range dataServer {
			strSql = fmt.Sprintf("dbscale enable dataserver %s", v.Servername)
			_, err = targetIns.exec_dml_stmt(strSql)
			if err != nil {
				return err
			}
		}
		flashback_log.Debug("enable donor node successfully")
		return nil
	}

	if cmd == "disable" {
		for _, i := range cloneIns {
			if i.Role == "donor" {
				for _, d := range dataServer {
					if d.MasterOnlineStatus == "Not_A_Master" && d.Host == i.MySQL_Ip {
						strSql = fmt.Sprintf("dbscale %s dataserver %s", cmd, d.Servername)
						_, err = targetIns.exec_dml_stmt(strSql)
						if err != nil {
							return err
						}
						flashback_log.Debug("disable donor node successfully")
						return nil
					}

				}
			}
		}
		return errors.New("donor not exists")
	}

	return nil
}

// 正确：disable donor node successfully


```





```sql
mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: database_1
                          host: 172.17.138.1
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: database_2
                          host: 172.17.138.0
                          port: 3308
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: database_3
                          host: 172.17.138.2
                          port: 3308
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
3 rows in set (0.00 sec)

```



#### 8、在ti路由节点执行：dbscale set global  "enable-read-only" = 0

```go
func enable_read_write(ins Node) error {
	strSql := "dbscale set global \"enable-read-only\" = 0"
	_, err := ins.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}
	return nil
}
```


### 5、write to gio_flashback_redo.log file successfully

```go
// write to gio_flashback_redo.log file successfully
	err = write_redo_file(target_nodes)
	if err != nil {
		return err
	}

///////////////////////////////
// 1，若gio_flashback_redo.log文件不存在，就创建
// 2，打开gio_flashback_redo.log文件
// 3，将target_nodes数据格式化为：json
// 4，截断gio_flashback_redo.log文件
// 5，将json化的target_nodes数据写入gio_flashback_redo.log文件
// 6，成功的日志为：write to gio_flashback_redo.log file successfully
func write_redo_file(ins interface{}) error {
	var filepath = "./log/gio_flashback_redo.log"
	if _, err := os.Stat(filepath); os.IsNotExist(err) {
		os.Create(filepath)
		os.Chmod(filepath, 0755)
	}
	redo_file, err := os.OpenFile("./log/gio_flashback_redo.log", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("open gio_flashback_redo.log file failed")
		return err
	}
	defer redo_file.Close()

	b, err := json.Marshal(&ins)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("Marshal json failed")
		return err
	}

	err = redo_file.Truncate(0)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("truncate gio_flashback_redo.log file failed")
		return err
	}

	bytes, err := redo_file.Write(b)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("write gio_flashback_redo.log file failed")
		return err
	}

	flashback_log.WithFields(logrus.Fields{
		"size": bytes,
	}).Debug("write to gio_flashback_redo.log file successfully")

	return nil
}

// 正常：
// {"level":"debug","msg":"write to gio_flashback_redo.log file successfully","size":1010,"time":"2023-09-20 21:51:35"}
```



### 6、donor数据节点安装clone插件并设置参数

-   执行：set global super_read_only=0
-   install plugin clone SONAME 'mysql_clone.so'
-   set global clone_autotune_concurrency = off
-   set global clone_buffer_size=33554432
-   set global clone_max_concurrency=32

```go


func donor_node_install_plugin(donor *Node) error {
	var p plugins
	strSql := "SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME='clone'"
	err := donor.get_single_row_data(&p, strSql)
	if err != nil && err.Error() != "sql: no rows in result set" {
		return err
	}

	if p.PluginStatus == "ACTIVE" {
		return nil
	}

	strSql = "set global super_read_only=0"
	_, err = donor.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "install plugin clone SONAME 'mysql_clone.so'"
	_, err = donor.exec_dml_stmt(strSql)
	if err != nil && err.Error() == "Function 'clone' already exists" {
		return err
	}

	strSql = "set global clone_autotune_concurrency = off"
	_, err = donor.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "set global clone_buffer_size=33554432"
	_, err = donor.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "set global clone_max_concurrency=32"
	_, err = donor.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	return nil
}



```



### 7、灾备的joiner、primary数据节点依次启动clone数据库

```go
// 1、ssh连接测试
// 2、mysql连接ping测试
// 3、执行：show variables like 'datadir'，获取数据目录最后一级目录
// 4、ssh连接执行：ps -ef | grep -v grep | grep mysqld | grep clonebackup | awk '{print $2}' | xargs
// 5、若存在clonebackup进程就kill -9
// 6、rm -rf /data/mysqldata/clonebackup/
// 7、mkdir -p /data/mysqldata/clonebackup/{logfile,dbdata,tmp,socket,pid}
// 8、cp /data/mysqldata/%s/*.conf /data/mysqldata/clonebackup/new.conf
// 9、修改配置文件：ckup/g' %s", datadir, "/data/mysqldata/clonebackup/new.conf
// 10、修改端口号：000 \n innodb_buffer_pool_size = 512MB' >> /data/mysqldata/clonebackup/new.conf
// 11、初始化：0.26/bin/mysqld --defaults-file=/data/mysqldata/clonebackup/new.conf --initialize-insecure --user=mysql
// 12、启动进程：pp/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/clonebackup/new.conf --user=mysql --datadir=/data/mysqldata/clonebackup/dbdata > /data/mysqldata/clonebackup/logfile/out.log 2>&1 &
// 13、create user if not exists root@'%' identified with mysql_native_password by 'root';grant all on *.* to root@'%' with grant option;
// /data/app/mysql-8.0.26/bin/mysql --defaults-file=/data/mysqldata/clonebackup/new.conf -uroot -e
//

func joiner_node_init(joinerCli *Node) error {
	if joinerCli.Client == nil {
		err := joinerCli.init_sys_con()
		if err != nil {
			return err
		}
	}

	if joinerCli.db == nil {
		err := joinerCli.init_mysql_conn()
		if err != nil {
			return err
		}
	}

	// TODO need to check
	var datadir string
	var v variables
	strSql := "show variables like 'datadir'"
	err := joinerCli.get_single_row_data(&v, strSql)
	if err != nil || v.Value == "" {
		return err
	}
	arr := strings.Split(v.Value, "/")
	datadir = arr[len(arr)-3]

	// 1) find joiner mysqld of process
	cmdStr := "ps -ef | grep -v grep | grep mysqld | grep clonebackup | awk '{print $2}' | xargs"
	result, err := joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 2) kill clonebackup mysqld process
	if result != "\n" {
		// kill clonebackup porcess
		cmdStr = fmt.Sprintf("kill -9  %s", result)
		_, err = joinerCli.Run(cmdStr)
		if err != nil {
			for i := 0; i < 3; i++ {
				_, err = joinerCli.Run(cmdStr)
				if err != nil {
					time.Sleep(3 * time.Second)
				} else {
					break
				}
				if i == 2 && err != nil {
					return err
				}
			}

		}

		time.Sleep(3 * time.Second)
	}

	// 3) remove clonebackup path
	cmdStr = "rm -rf /data/mysqldata/clonebackup/"
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 4) mkdir -p /data/mysqldata/clonebackup/{logfile,dbdata,tmp,socket,pid}
	cmdStr = "mkdir -p /data/mysqldata/clonebackup/{logfile,dbdata,tmp,socket,pid}"
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 5) cp /data/mysqldata/${array}/*.conf /data/mysqldata/clonebackup/new.conf
	cmdStr = fmt.Sprintf("cp /data/mysqldata/%s/*.conf /data/mysqldata/clonebackup/new.conf", datadir)
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 6) sed -i "s/${array}/clonebackup/g" ${cnfdir} > /data/mysqldata/clonebackup/new.conf
	cmdStr = fmt.Sprintf("sed -i 's/%s/clonebackup/g' %s", datadir, "/data/mysqldata/clonebackup/new.conf")
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 7) echo "port = 18000" >> /data/mysqldata/clonebackup/new.conf
	// echo "innodb_buffer_pool_size = 512MB" >> /data/mysqldata/clonebackup/new.conf
	cmdStr = "echo -e 'port = 18000 \n innodb_buffer_pool_size = 512MB' >> /data/mysqldata/clonebackup/new.conf"
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	// 8) /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/clonebackup/new.conf --initialize-insecure --user=mysql
	cmdStr = "/data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/clonebackup/new.conf --initialize-insecure --user=mysql"
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(5 * time.Second)

	// 9) nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/clonebackup/new.conf --user=mysql --datadir=/data/mysqldata/clonebackup/dbdata > /data/mysqldata/clonebackup/logfile/out.log 2>&1 &
	cmdStr = "nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/clonebackup/new.conf --user=mysql --datadir=/data/mysqldata/clonebackup/dbdata > /data/mysqldata/clonebackup/logfile/out.log 2>&1 &"
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(60 * time.Second)

	// 10) /data/app/mysql-8.0.26/bin/mysql --defaults-file=/data/mysqldata/16315fff-78c7-4f1d-91e4-729b0f76f801/my16315.conf
	// -uroot -e "create user if not exists root@'%' identified with mysql_native_password by '123456';grant all on *.* to root@'%' with grant option;"
	s := "create user if not exists root@'%' identified with mysql_native_password by 'root';grant all on *.* to root@'%' with grant option;"
	cmdStr = fmt.Sprintf("/data/app/mysql-8.0.26/bin/mysql --defaults-file=/data/mysqldata/clonebackup/new.conf -uroot -e %q ", s)
	_, err = joinerCli.Run(cmdStr)
	if err != nil {
		return err
	}

	time.Sleep(3 * time.Second)

	return nil
}
```





### 8、灾备的joiner、primary数据节点依次克隆灾备的donor数据节点数据

```go


// set global super_read_only=0
// install plugin clone SONAME 'mysql_clone.so'
// set global clone_autotune_concurrency = off
// set global clone_buffer_size=33554432
// set global clone_max_concurrency=32
// set global clone_valid_donor_list = 'donor_ip:donor_port'
// CLONE INSTANCE FROM '%s'@'%s':%s IDENTIFIED BY '%s'



func joiner_node_install_plugin_and_exec_clone(joiner *Node, donor *Node) error {
	if joiner.joinerdb == nil {
		err := joiner.init_joiner_node_conn()
		if err != nil {
			return err
		}
	}

	var p plugins
	strSql := "SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME='clone'"
	err := joiner.joiner_node_get_single_row_data(&p, strSql)
	if err != nil && err.Error() != "sql: no rows in result set" {
		return err
	}

	if p.PluginStatus == "ACTIVE" {

		strSql = "set global clone_autotune_concurrency = off"
		_, err = joiner.joiner_node_exec_dml_stmt(strSql)
		if err != nil {
			return err
		}

		strSql = "set global clone_buffer_size=33554432"
		_, err = joiner.joiner_node_exec_dml_stmt(strSql)
		if err != nil {
			return err
		}

		strSql = "set global clone_max_concurrency=32"
		_, err = joiner.joiner_node_exec_dml_stmt(strSql)
		if err != nil {
			return err
		}

		strSql = fmt.Sprintf("set global clone_valid_donor_list = '%s:%s'", donor.MySQL_Ip, donor.MySQL_Port)
		_, err = joiner.joiner_node_exec_dml_stmt(strSql)
		if err != nil {
			return err
		}

		strSql = fmt.Sprintf("CLONE INSTANCE FROM '%s'@'%s':%s IDENTIFIED BY '%s'", donor.MySQL_User, donor.MySQL_Ip, donor.MySQL_Port, donor.MySQL_Password)
		_, err = joiner.joiner_node_exec_dml_stmt(strSql)
		if err != nil {
			return err
		}
		return nil
	}

	strSql = "set global super_read_only=0"
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "install plugin clone SONAME 'mysql_clone.so'"
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "set global clone_autotune_concurrency = off"
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "set global clone_buffer_size=33554432"
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "set global clone_max_concurrency=32"
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = fmt.Sprintf("set global clone_valid_donor_list = '%s:%s'", donor.MySQL_Ip, donor.MySQL_Port)
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = fmt.Sprintf("CLONE INSTANCE FROM '%s'@'%s':%s IDENTIFIED BY '%s'", donor.MySQL_User, donor.MySQL_Ip, donor.MySQL_Port, donor.MySQL_Password)
	_, err = joiner.joiner_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	return nil
}






```









## 三、stop
{{<caution>}}
<!---->
成功标志：exec stop successfully
{{</caution>}}
```go
func Do_stop(sourceSocket string, targetSocket string, config string) error {
	cloneIns := make([]Node, 0)
	err := parse_flashback_config(config)
	if err != nil {
		return err
	}

	global_source_instance.MySQL_Ip = strings.Split(sourceSocket, ":")[0]
	global_source_instance.MySQL_Port = strings.Split(sourceSocket, ":")[1]
	global_source_instance.Database = "information_schema"

	global_target_instance.MySQL_Ip = strings.Split(targetSocket, ":")[0]
	global_target_instance.MySQL_Port = strings.Split(targetSocket, ":")[1]
	global_target_instance.Database = "information_schema"

	err = global_source_instance.init_mysql_conn()
	if err != nil {
		return err
	}

	err = global_target_instance.init_mysql_conn()
	if err != nil {
		return err
	}

	err = read_redo_file(&cloneIns)
	if err != nil {
		return err
	}

	err = enable_read_only(global_target_instance)
	if err != nil {
		return err
	}

	err = clone_recovery(global_target_instance, cloneIns)
	if err != nil {
		return err
	}

	err = add_back_cluster(global_source_instance, global_target_instance)
	if err != nil {
		return err
	}

	err = truncate_redo_file()
	if err != nil {
		return err
	}
	flashback_log.Info("exec stop successfully")
	return nil
}
```

### 1、解析配置文件：

1.  source
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port
2.  target
    1.  host
    2.  port
    3.  mysql_user
    4.  mysql_password
    5.  sys_user
    6.  sys_password
    7.  sys_port



{{<note>}}
<!---->
解析 flashback的配置文件：dr_flashback.ini文件

参数config不存在时，则默认参数配置文件为main函数所在目录下的dr_flashback.ini文件

解析参数时，特殊字符将会被忽略，比如#$，因此解析可能有错。因此尽量不要用特殊字符
{{</note>}}
```go
func parse_flashback_config(conf_file string) error {
	iniConfig, err := ini.Load(conf_file)
	if err != nil {
		return err
	}
	for _, sectionName := range iniConfig.SectionStrings() {
		if strings.Contains(sectionName, "source") {
			s := iniConfig.Section(sectionName)
			global_source_instance.MySQL_Ip = s.Key("host").String()
			global_source_instance.MySQL_Port = s.Key("port").String()
			global_source_instance.MySQL_User = s.Key("mysql_user").String()
			global_source_instance.MySQL_Password = s.Key("mysql_password").String()
			global_source_instance.SysUser = s.Key("sys_user").String()
			global_source_instance.SysPassword = s.Key("sys_password").String()
			global_source_instance.SysPort = s.Key("sys_port").String()
		}
		if strings.Contains(sectionName, "target") {
			s := iniConfig.Section(sectionName)
			global_target_instance.MySQL_Ip = s.Key("host").String()
			global_target_instance.MySQL_Port = s.Key("port").String()
			global_target_instance.MySQL_User = s.Key("mysql_user").String()
			global_target_instance.MySQL_Password = s.Key("mysql_password").String()
			global_target_instance.SysUser = s.Key("sys_user").String()
			global_target_instance.SysPassword = s.Key("sys_password").String()
			global_target_instance.SysPort = s.Key("sys_port").String()
		}
	}
	flashback_log.Info("parse flashback config successfully")
	return nil
}


```





### 2、si和ti节点创建数据库连接和ping
{{<note>}}
<!---->
global_source_instance的ip和port初始化为命令行参数-si的值

global_target_instance的ip和port初始化为命令行参数-si的值

init mysql connection with pass ...

根据节点的mysql连接信息，创建连接，然后ping

正常：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}

异常：mysql create connection failed 和 mysql ping connection failed

si节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:55:28"}

ti节点：{"level":"info","msg":"mysql ping connection successfully","time":"2023-09-19 22:58:18"}

{{</note>}}
```go
func (d *Node) init_mysql_conn() error {
	dbSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8", d.MySQL_User, d.MySQL_Password, d.MySQL_Ip, d.MySQL_Port, d.Database)
	db, err := sqlx.Open("mysql", dbSource)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql create connection failed")
		return err
	}

	if err = db.Ping(); err != nil {
		flashback_log.WithFields(logrus.Fields{
			"ip":       d.MySQL_Ip,
			"port":     d.MySQL_Port,
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql ping connection failed")
		return err
	}
	flashback_log.Info("mysql ping connection successfully")

	db.SetConnMaxIdleTime(time.Minute * 5)
	db.SetMaxOpenConns(1)
	d.db = db
	return nil
}


```





### 3、读取文件：gio_flashback_redo.log
{{<note>}}
<!---->
1. 打开文件

2. 读取文件内容

3. 判断文件是否为空

4. 解码json数据

错误：
- open gio_flashback_redo.log file failed
- read gio_flashback_redo.log file failed
- gio_flashback_redo.log file size empty
- Unmarshal json failed
{{</note>}}

```go
func read_redo_file(instances interface{}) error {
	redo_file, err := os.Open("./log/gio_flashback_redo.log")
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("open gio_flashback_redo.log file failed")
		return err
	}
	defer redo_file.Close()

	r := bufio.NewReader(redo_file)
	bytes, _, err := r.ReadLine()
	if err != nil && err != io.EOF {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("read gio_flashback_redo.log file failed")
		return err
	}

	line := string(bytes)
	if line == "" {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("gio_flashback_redo.log file size empty")
		return nil
	}

	err = json.Unmarshal(bytes, instances)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("Unmarshal json failed")
		return err
	}

	return nil
}
```



### 4、ti节点执行：enable-read-only = 1
{{<note>}}
<!---->
```sql
dbscale set global "enable-read-only" = 1
```
{{</note>}}
```go

func enable_read_only(ins Node) error {
	strSql := "dbscale set global \"enable-read-only\" = 1"
	_, err := ins.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}
	return nil
}

```

### 5、恢复

```go
err = clone_recovery(global_target_instance, cloneIns)
	if err != nil {
		return err
	}

func clone_recovery(targetIns Node, cloneIns []Node) error {
	var wg sync.WaitGroup

	err := judge_clone_finished(cloneIns)
	if err != nil {
		return err
	}

	for _, ins := range cloneIns {
		if ins.Role == "primary" || ins.Role == "joiner" {
			wg.Add(1)
			go func(i Node) {
				recovery_node(i)
				wg.Done()
			}(ins)
		}
	}

	wg.Wait()

	for _, ins := range cloneIns {
		if ins.Role == "donor" {
			err := disable_and_enable_donor_node(targetIns, cloneIns, "enable")
			if err != nil {
				return err
			}
		}
	}
	return nil
}


```

#### （1）通过比较gtid判断克隆是否已经完成
{{<note>}}
<!---->
-   灾备集群中角色为primary和joiner的克隆数据节点的数据库连接和ping（18000端口），donor节点（原数据节点端口）也会进行数据库连接和ping
-   灾备集群中角色为primary和joiner的克隆数据节点（18000端口）执行：show master status，获取Executed_Gtid_Set
-   通过比较灾备集群中角色为primary和joiner的克隆数据节点、donor节点，这三者的的Executed_Gtid_Set比较是否都相同来判断是否已经克隆完毕
{{</note>}}

```go
func judge_clone_finished(cloneIns []Node) error {

	var gtidSet string
	for _, ins := range cloneIns {
		if ins.Role == "primary" || ins.Role == "joiner" {
			err := ins.init_recovery_conn()
			if err != nil {
				return err
			}

			var masterStatus MasterStatus
			strSql := "show master status"
			err = ins.recovery_node_get_single_row_data(&masterStatus, strSql)
			if err != nil {
				flashback_log.WithFields(logrus.Fields{
					"errMsg": err.Error(),
				}).Error("exec failed")
				return err
			}
			if gtidSet == "" {
				gtidSet = masterStatus.ExecutedGtidSet
			} else if gtidSet != masterStatus.ExecutedGtidSet {
				err = errors.New("clone incomplete")
				flashback_log.WithFields(logrus.Fields{
					"errMsg": err.Error(),
				}).Error("exec failed")
				return err
			}

		}
		if ins.Role == "donor" {
			err := ins.init_mysql_conn()
			if err != nil {
				return err
			}
			var masterStatus MasterStatus
			strSql := "show master status"
			err = ins.get_single_row_data(&masterStatus, strSql)
			if err != nil {
				flashback_log.WithFields(logrus.Fields{
					"errMsg": err.Error(),
				}).Error("exec failed")
				return err
			}
			if gtidSet == "" {
				gtidSet = masterStatus.ExecutedGtidSet
			} else if gtidSet != masterStatus.ExecutedGtidSet {
				err = errors.New("clone incomplete")
				flashback_log.WithFields(logrus.Fields{
					"errMsg": err.Error(),
				}).Error("exec failed")
				return err
			}
		}
	}

	return nil
}
```

{{<note>}}
<!---->
灾备集群中角色为primary和joiner的克隆数据节点的数据库连接和ping：
{{</note>}}

```go
func (d *Node) init_recovery_conn() error {
	dbSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=utf8", d.MySQL_User, d.MySQL_Password, d.MySQL_Ip, d.JoinerPort, d.Database)
	db, err := sqlx.Open("mysql", dbSource)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"ip":       d.MySQL_Ip,
			"port":     d.JoinerPort,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql create recovery connection failed")
		return err
	}

	if err = db.Ping(); err != nil {
		flashback_log.WithFields(logrus.Fields{
			"username": d.MySQL_User,
			"password": d.MySQL_Password,
			"ip":       d.MySQL_Ip,
			"port":     d.JoinerPort,
			"database": d.Database,
			"errMsg":   err.Error(),
		}).Error("mysql ping failed")
		return err
	}
	flashback_log.Info("mysql ping recovery connection successfully")

	db.SetConnMaxIdleTime(time.Minute * 5)
	db.SetMaxOpenConns(1)
	d.recoverydb = db
	return nil
}

```

```sql
mysql> show master status\G
*************************** 1. row ***************************
             File: binlog.000014
         Position: 548
     Binlog_Do_DB:
 Binlog_Ignore_DB: dbscale_tmp
Executed_Gtid_Set: b9da174e-5208-11ee-8c26-00163e2d01d5:1-53,
ba09b02c-5208-11ee-b5af-00163ed307b5:1-114500,
ba245d88-5208-11ee-aae7-00163ee05610:1-4,
f9958bc0-5604-11ee-a5a1-00163ea7a91c:1-4,
f9f2f2be-5604-11ee-b389-00163e3cdeea:1-20
1 row in set (0.00 sec)

```

#### （2）先并行恢复primary和joiner节点
{{<note>}}
<!---->
-   primary和joiner数据节点的3308端口实例和18000端口实例依次执行：shutdown
-   保证ssh连接可用
-   删除旧的dbdata_bak数据目录：Sprintf("rm -rf /data/mysqldata/%s/dbdata_bak", ins.DataDir)
-   重命令当前的数据目录为dbdata >dbdata_bak：Sprintf("mv /data/mysqldata/%s/dbdata /data/mysqldata/%s/dbdata_bak", ins.DataDir, ins.DataDir)
-   将克隆的数据目录替换到数据节点的3308端口实例的数据目录：Sprintf("mv /data/mysqldata/clonebackup/dbdata /data/mysqldata/%s/", ins.DataDir)
-   启动数据节点：nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-78c7-4f1d-91e4-729b0f76f801/my16315.conf\n --user=mysql --datadir=/data/mysqldata/16315fff-78c7-4f1d-91e4-729b0f76f801/dbdata > /data/mysqldata/16315fff-78c7-4f1d-91e4-729b0f76f801/logfile/out.log 2>&1 &
-   清理clone目录：rm -rf /data/mysqldata/clonebackup
-   测试数据节点是否可连接与ping：ins.init_mysql_conn()
-   测试数据节点是否可select：SELECT 'v' as Variable_name, 1 as Value
{{</note>}}


```go
func recovery_node(ins Node) error {
	err := shutdown_instance(&ins)
	if err != nil {
		return err
	}

	time.Sleep(30 * time.Second)

	if ins.Client == nil {
		err := ins.init_sys_con()
		if err != nil {
			return err
		}
	}

	time.Sleep(5 * time.Second)
	strCmd := fmt.Sprintf("rm -rf /data/mysqldata/%s/dbdata_bak", ins.DataDir)
	_, err = ins.Run(strCmd)
	if err != nil {
		return err
	}

	time.Sleep(5 * time.Second)
	strCmd = fmt.Sprintf("mv /data/mysqldata/%s/dbdata /data/mysqldata/%s/dbdata_bak", ins.DataDir, ins.DataDir)
	_, err = ins.Run(strCmd)
	if err != nil {
		return err
	}

	time.Sleep(5 * time.Second)
	strCmd = fmt.Sprintf("mv /data/mysqldata/clonebackup/dbdata /data/mysqldata/%s/", ins.DataDir)
	_, err = ins.Run(strCmd)
	if err != nil {
		return err
	}

	time.Sleep(5 * time.Second)
	confDir := "/data/mysqldata/" + ins.DataDir + "/my" + (ins.DataDir)[0:5] + ".conf"
	strCmd = fmt.Sprintf("nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=%s --user=mysql --datadir=/data/mysqldata/%s/dbdata > /data/mysqldata/%s/logfile/out.log 2>&1 &", confDir, ins.DataDir, ins.DataDir)
	_, err = ins.Run(strCmd)
	if err != nil {
		return err
	}

	time.Sleep(5 * time.Second)
	strCmd = "rm -rf /data/mysqldata/clonebackup"
	_, err = ins.Run(strCmd)
	if err != nil {
		return err
	}

	time.Sleep(30 * time.Second)

	for i := 0; i < 5; i++ {
		err = ins.init_mysql_conn()
		if err != nil {
			flashback_log.WithFields(logrus.Fields{
				"errMsg": err.Error(),
				"cmd":    strCmd,
				"retry":  i,
			}).Error(" create joiner connection failed! ")
		}

		err = ping_and_select(&ins)
		if err != nil {
			flashback_log.WithFields(logrus.Fields{
				"errMsg": err.Error(),
				"cmd":    strCmd,
				"retry":  i,
			}).Error(" ping or Select DB failed! ")
		} else {
			break
		}
		time.Sleep(30 * time.Second)
	}
	return nil
}


```
{{<note>}}
<!---->
- 保证primary和joiner数据节点的3308端口实例和18000端口实例都正常连接
- primary和joiner数据节点的3308端口实例和18000端口实例依次执行：shutdown
{{</note>}}

```go
func shutdown_instance(ins *Node) error {
	if ins.db == nil {
		err := ins.init_mysql_conn()
		if err != nil {
			return err
		}
	}

	if ins.joinerdb == nil {
		err := ins.init_recovery_conn()
		if err != nil {
			return err
		}
	}

	strSql := "shutdown"
	_, err := ins.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	_, err = ins.recovery_node_exec_dml_stmt(strSql)
	if err != nil {
		return err
	}
	return nil
}

// verify 1)
func ping_and_select(ins *Node) error {
	var v variables
	strSql := "SELECT 'v' as Variable_name, 1 as Value "
	err := ins.get_single_row_data(&v, strSql)
	if err != nil {
		return err
	}
	if v.Value == "1" {
	} else {
		return errors.New("select failed")
	}
	return nil
}

```





#### （3）后恢复donor节点：
{{<note>}}
<!---->
```sql
dbscale enable dataserver 所有dataserver ;
```
-   先判断ti路由节点是否可连接
-   遍历灾备数据节点，并enable
{{</note>}}

```go
for _, ins := range cloneIns {
		if ins.Role == "donor" {
			err := disable_and_enable_donor_node(targetIns, cloneIns, "enable")
			if err != nil {
				return err
			}
		}
	}

func disable_and_enable_donor_node(targetIns Node, cloneIns []Node, cmd string) error {
	if targetIns.db == nil {
		return errors.New("connection is nil")
	}

	var dataServer []dataServers

	strSql := "dbscale show dataservers"
	err := targetIns.get_multi_row_data(&dataServer, strSql)
	if err != nil {
		return err
	}

	if len(dataServer) == 0 {
		return errors.New(" dataServer length = 0 ")
	}

	if cmd == "enable" {
		for _, v := range dataServer {
			strSql = fmt.Sprintf("dbscale enable dataserver %s", v.Servername)
			_, err = targetIns.exec_dml_stmt(strSql)
			if err != nil {
				return err
			}
		}
		flashback_log.Debug("enable donor node successfully")
		return nil
	}

	if cmd == "disable" {
		for _, i := range cloneIns {
			if i.Role == "donor" {
				for _, d := range dataServer {
					if d.MasterOnlineStatus == "Not_A_Master" && d.Host == i.MySQL_Ip {
						strSql = fmt.Sprintf("dbscale %s dataserver %s", cmd, d.Servername)
						_, err = targetIns.exec_dml_stmt(strSql)
						if err != nil {
							return err
						}
						flashback_log.Debug("disable donor node successfully")
						return nil
					}

				}
			}
		}
		return errors.New("donor not exists")
	}

	return nil
}
```

### 6、生产集群路由节点添加灾备集群路由节点：
{{<note>}}
<!---->
-   在生产集群的路由节点上执行：dbscale request next group id
-   添加dataserver：Sprintf("dbscale dynamic ADD DATASERVER server_name=slave_dbscale_server,server_host=\"%s\",server_port=%s,server_user=\"%s\",server_password=\"%s\",dbscale_server", target.MySQL_Ip, target.MySQL_Port, target.MySQL_User, target.MySQL_Password)
-   添加datasource：Sprintf("dbscale dynamic add server datasource slave_dbscale_source slave_dbscale_server-1-1000-400-800 group_id = %s", strconv.Itoa(ngi.Id))
-   添加slave_dbscale_source：dbscale dynamic add slave slave_dbscale_source to normal_0
{{</note>}}

```go
err = add_back_cluster(global_source_instance, global_target_instance)
	if err != nil {
		return err
	}

func add_back_cluster(source Node, target Node) error {
	if source.db == nil {
		return errors.New("source db is nil")
	}

	var ngi groupId
	var c []requestClusterInfo

	strSql := "dbscale request cluster info"
	err := source.get_multi_row_data(&c, strSql)
	if err != nil {
		return err
	}

	if len(c) > 0 {
		for _, v := range c {
			if v.MasterDbscale == "master" {
				var tmpIns Node
				tmpIns.MySQL_User = source.MySQL_User
				tmpIns.MySQL_Password = source.MySQL_Password
				s := strings.Split(v.Host, ":")
				tmpIns.MySQL_Ip = s[0]
				tmpIns.MySQL_Port = s[1]
				tmpIns.Database = source.Database

				err = tmpIns.init_mysql_conn()
				if err != nil {
					return err
				}
				strSql = "dbscale request next group id"
				err = tmpIns.get_single_row_data(&ngi, strSql)
				if err != nil {
					return err
				}
			}
		}
	}

	strSql = fmt.Sprintf("dbscale dynamic ADD DATASERVER server_name=slave_dbscale_server,server_host=\"%s\",server_port=%s,server_user=\"%s\",server_password=\"%s\",dbscale_server", target.MySQL_Ip, target.MySQL_Port, target.MySQL_User, target.MySQL_Password)
	_, err = source.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = fmt.Sprintf("dbscale dynamic add server datasource slave_dbscale_source slave_dbscale_server-1-1000-400-800 group_id = %s", strconv.Itoa(ngi.Id))
	_, err = source.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	strSql = "dbscale dynamic add slave slave_dbscale_source to normal_0"
	_, err = source.exec_dml_stmt(strSql)
	if err != nil {
		return err
	}

	return nil
}
```

```sql
mysql> dbscale request cluster info\G
*************************** 1. row ***************************
          master_dbscale: slave
       cluster_server_id: 3
                    host: 172.17.138.0:3307
               join time: 2023-09-21 15:02:27
         ka init Version: 7
       ka update Version: 9|9
    dynamic node Version: 1
   dynamic space Version: 6
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
*************************** 2. row ***************************
          master_dbscale: master
       cluster_server_id: 4
                    host: 172.17.138.1:3307
               join time: 2023-09-21 15:30:05
         ka init Version: 9
       ka update Version: 9|9
    dynamic node Version: 1
   dynamic space Version: 6
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
*************************** 3. row ***************************
          master_dbscale: slave
       cluster_server_id: 5
                    host: 172.17.138.2:3307
               join time: 2023-09-21 15:53:32
         ka init Version: 9
       ka update Version: 9|1
    dynamic node Version: 1
   dynamic space Version: 6
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
3 rows in set (0.07 sec)

mysql> dbscale request next group id ;
+---------------+
| next_group_id |
+---------------+
| 1             |
+---------------+
1 row in set (0.01 sec)

```

### 7、截断gio_flashback_redo.log文件为空

```go
	err = truncate_redo_file()
	if err != nil {
		return err
	}
	flashback_log.Info("exec stop successfully")
	return nil

func truncate_redo_file() error {
	filepath := "./log/gio_flashback_redo.log"
	file, err := os.OpenFile(filepath, os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"errMsg": err.Error(),
		}).Error("open gio_flashback_redo.log file failed")
		return err
	}
	defer file.Close()

	err = file.Truncate(0)
	if err != nil {
		flashback_log.WithFields(logrus.Fields{
			"filepath": filepath,
			"errMsg":   err.Error(),
		}).Error("truncate file failed")
		return err
	}
	flashback_log.WithFields(logrus.Fields{
		"filepath": filepath,
	}).Debug("truncate file successfully")
	return nil
}
```































