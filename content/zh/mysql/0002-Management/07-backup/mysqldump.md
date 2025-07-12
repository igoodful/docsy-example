---
title: mysqldump
description: mysqldump
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


```sql
-- MySQL dump 10.13  Distrib 8.0.32, for Linux (aarch64)
--
-- Host: 127.0.0.1    Database: apple
-- ------------------------------------------------------
-- Server version       8.0.32

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8mb4 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN;
SET @@SESSION.SQL_LOG_BIN= 0;

--
-- GTID state at the beginning of the backup
--

SET @@GLOBAL.GTID_PURGED=/*!80000 '+'*/ 'f72aadcd-1320-11ef-b063-00163e0bbef4:1-348,
f72aadcd-1320-11ef-b063-00163e0bbef8:1-346,
f7737dc1-1320-11ef-8c7b-00163e311b0d:1-23';

--
-- Current Database: `apple`
--

CREATE DATABASE /*!32312 IF NOT EXISTS*/ `apple` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */;

USE `apple`;

--
-- Table structure for table `sbtest1`
--

DROP TABLE IF EXISTS `sbtest1`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `sbtest1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB AUTO_INCREMENT=100001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `sbtest1`
--

LOCK TABLES `sbtest1` WRITE;
/*!40000 ALTER TABLE `sbtest1` DISABLE KEYS */;
INSERT INTO `sbtest1` VALUES (1,64614,'68487932199-96439406143-93774651418-41631865787-96406072701-20604855487-25459966574-28203206787-41238978918-19503783441','22195207048-70116052123-74140395089-76317954521-98694025897');

```

建议删掉这些
- `SET @@SESSION.SQL_LOG_BIN= 0;` 不计入binlog日志，这个在导入到集群时，一定要删掉或者改成1，否则主从会断开
- `SET @@GLOBAL.GTID_PURGED` 设置GTID_PURGED
- `/*!50503 SET NAMES utf8mb4 */;` 设置字符集
- `/*!40103 SET TIME_ZONE='+00:00' */;` 设置时区

所以在我们做主从用了gtid时，用mysqldump备份时就要加`--set-gtid-purged=off`，否则你在主上导入恢复了数据，主没有了binlog日志，同步则不会被同步。







## 1. 选项

| 参数名                          | 缩写 | 含义                          |
|---------------------------------|------|-----------------------------|
| --host                          | -h   | 服务器IP地址                  |
| --port                          | -P   | 服务器端口号                  |
| --user                          | -u   | MySQL用户名                   |
| --password                      | -p   | MySQL密码                     |
| --login-path                    | -p   | MySQL密码                     |
| --databases                     | -B   | 指定要备份的数据库            |
| --all-databases                 | -A   | 备份mysql服务器上的所有数据库 |
| --all-tablespaces               | -Y   | 备份mysql服务器上的所有表空间 |
| --compact                       |      | 压缩模式，产生更少的输出       |
| --comments                      |      | 添加注释信息                  |
| --complete-insert               |      | 输出完成的插入语句            |
| --lock-tables                   |      | 备份前，锁定所有数据库表       |
| --no-create-db/--no-create-info |      | 禁止生成创建数据库语句        |
| --force                         |      | 当出现错误时仍然继续备份操作  |
| --default-character-set         |      | 指定默认字符集                |
| --add-locks                     |      | 备份数据库表时锁定数据库表    |
| --triggers                      |      | 导出触发器                    |
| --triggers                      |      | 导出触发器                    |
| --events                        | -E   | 导出事件                      |
| --routines                      | -R   | 导出存储过程以及自定义函数    |
| --where                         | -w   | 导出某张表部分数据的where条件 |



## 备份

```bash
/usr/bin/time mysqldump -umsandbox -pmsandbox -h127.0.0.1 -P8021 test | gzip > db.sql.gz

/usr/bin/time mysqlpump --default-parallelism=4 -umsandbox -pmsandbox -h127.0.0.1  -P8021 test | gzip > db2.sql.gz
```





## 恢复

```bash
/usr/bin/time gzip -d < db.sql.gz | ./use test
```












### 解析指定时间段的慢日志
```sql
sed -n '/Time: 2022-12-20/,/Time: 2022-12-21/p' /data/mysql/slow.log | mysqldumpslow -s at -n 10 - | more
sed -n '/Time: 2022-12-20/,/Time: 2022-12-21/p' /data/mysql/slow.log | mysqldumpslow -s at -n 10 - | more
sed -n '/Time: 2022-12-20/,/Time: 2022-12-21/p' /data/mysql/slow.log | mysqldumpslow -s at -n 10 - | more
```


## 备份用来恢复丢失数据案例


#### 步骤

```viz-dot
digraph "M" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
                ];
        "M" -> "S1";
        "M" -> "S2";

}
```

> 逻辑备份或物理备份都可以


| 时间 | 步骤                                | 示例                                                             |
|:-----|:----------------------------------|:-----------------------------------------------------------------|
| `t1` | 一直往集群写入数据，此时先写入实例M  | sysbench                                                         |
| `t2` | 备份实例S1                          | `mysqldump --single-transaction --master-data=2 --all-databases` |
| `t3` | 主库实例M宕机                       | 关掉主库所在机器                                                 |
| `t4` | 集群选取实例S1作为主库              |                                                                  |
| `t5` | 一直往集群写入数据，此时先写入实例S1 | sysbench                                                         |
| `t6` | 宕机的实例M恢复，启动实例            |                                                                  |
| `t7` | 主库M change 到实例S1               | `change master to S1`                                            |
| `t8` | 丢失了部分的数据                    |                                                                  |







```bash
mysqldump -uroot -p123456 --single-transaction --master-data=2 --all-databases > dump.sql


mysql -uroot -p123456 < dump.sql
```






























