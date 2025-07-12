---
title: 创建集群
description: 创建集群
date: 2017-01-05
weight: 30000


---

{{< alert >}}

1. 免费开源，易于学习

2. 适用MySQL

3. 轻量级数据库客户端软件，拥有图形化界面

{{< /alert >}}




### 新建集群

#### 1. 创建项目名称
```mermaid
graph LR
工作台-->访问控制-->项目-->创建
```

#### 2. 添加计算节点
```mermaid
graph LR
工作台-->物理资源-->资源列表-->节点-->添加计算节点-->填写相关信息-->测试连接-->提交
```

#### 3. 添加主机组
```mermaid
graph LR
工作台-->物理资源-->资源列表-->主机组-->创建主机组-->右键主机组编辑添加节点
```

#### 4. 关闭RDS锁监控采集

```mermaid
graph LR
工作台-->平台设置-->设置监控采集项
```

> - INNODB_LOCK_WAITS
>
> - INNODB_LOCKS
>
> - DATA_LOCK_WAITS
>
> - DATA_LOCKS
>

#### 5. 添加参数模板
```mermaid
graph LR
数据库服务-->模板管理-->参数模板管理-->创建自定义模板
```
> - innodb_buffer_pool_size
>
> - innodb_buffer_pool_instances
>
> - restrict-auth-source-top
>
> - local_infile
>

#### 6. 添加集群模板
```mermaid
graph LR
数据库服务-->模板管理-->参数模板管理-->创建自定义模板
```

创建完成后若不可用 移动鼠标至模板状态查看原因 若包找不到 右键编辑进入设置 将本地正确的包名修改至配置 默认位置：

- GreatDBRouter与zookeeper路径：/data/web_repo/data/repo/arm64/static/stable/cluster4/

- MySQL路径：/data/web_repo/data/repo/arm64/static/stable/mysql8.0

#### 7. 创建集群
至少需要两个节点
```mermaid
graph LR
数据库服务-->集群管理-->创建集群-->创建新集群
```

#### 8. 添加备份服务器
```mermaid
graph LR
数据库服务-->备份中心-->备份服务器-->添加备份服务器
```

#### 9. 添加备份计划
```mermaid
graph LR
数据库服务-->备份中心-->备份计划-->添加备份计划
```

#### 10. 配置监控
```mermaid
graph LR
数据库服务-->模板管理-->一键告警模板-->创建自定义模板
```

#### 11. 添加binlog定时备份任务
```mermaid
graph LR
数据库服务-->定时器-->创建定时器
```

```mermaid
graph LR
点击创建的定时器-->任务-->创建
```







































