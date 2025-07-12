---
title: WireShark
description: WireShark
date: 2023-10-30
weight: 30000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<note>}}



{{</note>}}




## 恢复默认设置


帮助》关于wireshark》文件夹》个人配置》删除C:\Users\12626\AppData\Roaming\Wireshark下面的所有文件（除C:\Users\12626\AppData\Roaming\Wireshark\profiles外）






## 配置时间显示格式


视图》时间显示格式》UTC 日期和时间



## 列操作


### 添加列

1. 编辑》首选项》外观》列》+》



### 隐藏列

任意列名称上右键

### 删除列

对应列上右键


在列名称上右键》选择需要显示或隐藏的列或删除列

注意：隐藏字段时，在列名栏的任意位置右键即可；而删除字段时，需要在指定的列名位置右键，以防误删。






## 显示过滤器

用来过滤包，去掉不需要不关心的包

显示过滤器的语法包含5个核心元素：IP、端口、协议、比较运算符和逻辑运算符。

1. IP地址：ip.addr、ip.src、ip.dst

2. 端口：tcp.port、tcp.srcport、tcp.dstport

3. 协议：tcp、udp、http

4. 比较运算符：> < == >= <= !=

5. 逻辑运算符：and、or、not、xor（有且仅有一个条件被满足）


5个核心元素可以自由组合，比如：

ip.addr == 192.168.32.121：显示IP地址为 192.168.32.121 的数据包

tcp.port == 80 ：显示端口为 80 的数据包






## TCP





### TCP首部



| 名称     | 字段                  | 大小（字节） |   |
|:---------|:----------------------|:-----------|:--|
| 源端口   | tcp.srcport           | 2          |   |
| 目的端口 | tcp.dstport           | 2          |   |
| 序列号   | tcp.seq               | 4          |   |
| 确认号   | tcp.ack               | 4          |   |
| 窗口大小 | tcp.window_size_value | 2          |   |
| 校验和   | tcp.checksum          | 2          |   |
| 紧急指针 | tcp.urgent_pointer    | 2          |   |
| 标志位   |                       |            |   |
| 选项     | tcp.options           |            |   |
| 数据     | data.data             |            |   |
|          |                       |            |   |
|          |                       |            |   |
|          |                       |            |   |
|          |                       |            |   |
|          |                       |            |   |
|          |                       |            |   |
































