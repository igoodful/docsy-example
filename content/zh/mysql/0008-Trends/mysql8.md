---
title: MySQL 8.0 新特性
description: MySQL5.7 在2023年10月21日后将停服.
date: 2023-10-10
weight: 80000


---

{{< alert >}}
mysqld-auto.cnf



reset persist
{{< /alert >}}



{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}

### 全局变量的修改


在8之前的版本中，对于全局变量的修改，其只会影响其内存值，而不会持久化到配置文件中。数据库重启，又会恢复成修改前的值。

从8开始，可通过SET PERSIST命令将全局变量的修改持久化到配置文件中。

```sql
set persist max_connections=10240;

set persist max_connections=default;

reset persist;

select * from performance_schema.persisted_variables;
```

安装 jq
```sql
yum -y install jq
```

#### 查看 mysqld-auto.cnf
```sql




[root@k8s-node-77 data]# cat mysqld-auto.cnf |python -m json.tool
{
    "Version": 1,
    "mysql_server": {
        "max_connections": {
            "Metadata": {
                "Host": "",
                "Timestamp": 1699613762483302,
                "User": "admin"
            },
            "Value": "10120"
        },
        "mysql_server_static_options": {
            "group_replication_enforce_update_everywhere_checks": {
                "Metadata": {
                    "Host": "localhost",
                    "Timestamp": 1684857280268927,
                    "User": "mysql.session"
                },
                "Value": "OFF"
            },
            "group_replication_single_primary_mode": {
                "Metadata": {
                    "Host": "localhost",
                    "Timestamp": 1684857280269735,
                    "User": "mysql.session"
                },
                "Value": "ON"
            }
        }
    }
}

[root@k8s-node-77 data]# cat mysqld-auto.cnf |jq
{
  "Version": 1,
  "mysql_server": {
    "max_connections": {
      "Value": "10120",
      "Metadata": {
        "Timestamp": 1699613762483302,
        "User": "admin",
        "Host": ""
      }
    },
    "mysql_server_static_options": {
      "group_replication_enforce_update_everywhere_checks": {
        "Value": "OFF",
        "Metadata": {
          "Timestamp": 1684857280268927,
          "User": "mysql.session",
          "Host": "localhost"
        }
      },
      "group_replication_single_primary_mode": {
        "Value": "ON",
        "Metadata": {
          "Timestamp": 1684857280269735,
          "User": "mysql.session",
          "Host": "localhost"
        }
      }
    }
  }
}

```



