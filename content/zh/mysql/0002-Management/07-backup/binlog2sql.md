---
title: binlog2sql
description: binlog2sql
date: 2023-10-26
weight: 20000
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert color="danger" title="注意" >}}

- mysql实例必须开启，不能离线模式下解析
- 参数 binlog_row_image 必须为FULL，暂不支持MINIMAL
- [binlog2sql](https://www.cnblogs.com/igoodful/p/13565557.html)

{{</alert>}}




## 一、安装

```bash
git clone https://github.com/danfengcao/binlog2sql.git && cd binlog2sql

pip install -r requirements.txt

pip uninstall pymysql

pip install pymysql~=0.9.3

```

## 二、参数

| 参数名             | 缩写  | 默认值                     | 含义                                                                             |
| :----------------- | :---- | :------------------------- | -------------------------------------------------------------------------------- |
| `--host`           | `-h`  |                            | 服务器IP地址                                                                     |
| `--port`           | `-P`  |                            | 服务器端口号                                                                     |
| `--user`           | `-u`  |                            | MySQL用户名                                                                      |
| `--password `      | `-p`  |                            | MySQL密码                                                                        |
| `--databases`      | `-d ` | 空                         | 只解析目标库，多个库用空格隔开，如-d db1 db2                                     |
| `--tables`         | `-t`  | 空                         | 只解析目标表，多张表用空格隔开，如-t tbl1 tbl2                                   |
| `--sql-type`       |       | 增删改都解析               | 多个类型用空格隔开，如--sql-type INSERT DELETE                                   |
| `--only-dml`       |       | false                      | 只解析dml，忽略ddl                                                               |
| `--start-file`     |       |                            | 必须指定的起始解析文件名                                                         |
| `--stop-file`      |       | 默认为start-file同一个文件 | 终止解析文件，                                                                   |
| `--start-position` |       | 默认为start-file的起始位置 | 起始解析位置                                                                     |
| `-stop-position`   |       | 默认为stop-file的最末位置  | 终止解析位置                                                                     |
| `--start-datetime` |       | 默认不过滤                 | 起始解析时间 ，格式`%Y-%m-%d %H:%M:%S`                                           |
| `--stop-datetime`  |       | 默认不过滤                 | 终止解析时间，格式`%Y-%m-%d %H:%M:%S`                                            |
| `--flashback `     | `-B`  | false                      | 生成回滚SQL，可解析大文件，不受内存限制,与stop-never或no-primary-key不能同时添加 |
| `--no-primary-key` |       | false                      | 对INSERT语句去除主键                                                             |
| `--stop-never`     |       | false                      | 持续解析binlog,同步至执行命令时最新的binlog位置                                  |
| `--back-interval ` |       | 1                          | 每打印一千行回滚SQL，加一句SLEEP多少秒，如不想加SLEEP，请设为0。可选。默认1.0    |

- `--sql-type`：只解析指定类型，支持INSERT, UPDATE, DELETE。多个类型用空格隔开，如--sql-type INSERT DELETE。可选。默认为增删改都解析。用了此参数但没填任何类型，则三者都不解析。


## 三、使用




#### 1. 获取需要回滚的位点段

```bash
mysqlbinlog -vv --base64-output=decode-rows bin.000052

```



#### 2. 生成回滚sql

```bash
./binlog2sql.py -uroot -p123456  -h127.0.0.1 -P3307 -dapple -tevent --start-file='bin.000144'  --start-position=280 --stop-position=3419 -B  > glc.log

./binlog2sql.py -uroot -p123456  -h127.0.0.1 -P3307 -dapple -tevent --start-file='bin.000143'  --start-datetime='2020-09-09 11:44:00' --stop-datetime='2020-09-09 11:50:00'  -B  > glc.log


```





#### 3. 确认回滚sql正确

```bash
mysql -h127.0.0.1 -P3306 -uroot -p123456 apple < rollback.sql

```


































