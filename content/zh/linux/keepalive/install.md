---
title: install
description: install
date: 2023-10-24
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert>}}

ddd

dd


{{</alert>}}

| ip              | 功能                          | 备注                      |
| :-------------- | :---------------------------- | :------------------------ |
| 192.168.247.133 | GreatDBCluster5.0集群机器     | Real Server               |
| 192.168.247.134 | GreatDBCluster5.0集群机器     | Real Server               |
| 192.168.247.135 | GreatDBCluster5.0集群机器     | Real Server               |
| 192.168.247.136 | LVS+Keepalived负载均衡机器    | Director Server           |
| 192.168.247.137 | LVS+Keepalived负载均衡机器    | Director Server           |
| 192.168.247.200 | VIP，不占用机器               | 挂在两台Director Server上 |
| 192.168.247.166 | 前置机，测试通过VIP访问数据库 | 测试机器                  |

- Real Server和Director Server的每个节点都需要设置VIP。
  - 在Real Server上，VIP一般绑定在回环地址上，例如lo:0；
  - 在Director Server上，VIP绑定在真实的网络接口设备上，例如eth0:0。


## 配置Real Server

**数据库超时时间**：
- `interactive_timeout`：交互式连接的最大空闲时长
- `wait_timeout`：非交互式连接的最大空闲时长
如果是交互式连接的session那么`wait_timeout`将被`interactive_timeout`覆盖。建议`interactive_timeout`和`wait_timeout`设置相同的数值。

### 安装`net-tools`
```bash
yum install -y net-tools
```

### 关闭防火墙和SELINUX
```bash
systemctl status firewalld
systemctl stop firewalld
systemctl disable firewalld
```

### 关闭SELINUX
> vi /etc/selinux/config  #修改 SELINUX=disabled



### 手动测试添加和删除VIP，以确认可以正常操作

1. 手动添加VIP
```bash
/sbin/ip addr add 192.168.247.200/24 dev eth0
```
2. 手动删除VIP
```bash
/sbin/ip addr del 192.168.247.200/24 dev eth0
```

### 配置`VIP`为`192.168.247.200`


`cat /root/sh/realserver.sh start`


### 添加自启动脚本：`/root/sh/realserver.sh`
1. 加入开机启动
在`/etc/rc.d/rc.local`末尾添加

- `cat  /etc/rc.d/rc.local`
```shell
#!/bin/bash
/var/lib/gip/gip.sh > /var/lib/gip/log.log
/root/sh/realserver.sh start
```

- `cat /root/sh/realserver.sh`
```shell
#!/bin/bash
VIP="192.168.247.200"
# shellcheck source=/dev/null
. /etc/rc.d/init.d/functions

case "$1" in

start)
        /sbin/ifconfig lo:0 $VIP netmask 255.255.255.255 broadcast $VIP
        /sbin/route add -host $VIP dev lo:0
        echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
        echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
        echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
        echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
        sysctl -p >/dev/null 2>&1
        echo "realserver.sh start"
        ;;
stop)
        /sbin/ifconfig lo:0 down
        /sbin/route del $VIP >/dev/null 2>&1
        echo "0" >/proc/sys/net/ipv4/conf/lo/arp_ignore
        echo "0" >/proc/sys/net/ipv4/conf/lo/arp_announce
        echo "0" >/proc/sys/net/ipv4/conf/all/arp_ignore
        echo "0" >/proc/sys/net/ipv4/conf/all/arp_announce
        sysctl -p >/dev/null 2>&1
        echo "realserver.sh stop"
        ;;
*)
        echo "Usage: $0 {start|stop}"
        exit 1
        ;;

esac

```

- `arp_ignore=1`：只回答目标IP地址是来访网络接口本地地址的ARP查询请求。RS不响应Client发出的ARP请求，RS只响应LVS发出的ARP请求
- `arp_announce=2`：对查询目标使用最适当的本地地址。RS直接返回给Client



- `cat /var/lib/gip/gip.sh`
```shell
path="/var/lib/gip/"
        flag_file="first_boot.flag"
        file=$path$flag_file
        if [ ! -f "$file" ]; then
          echo "$file not exist"
          hostnamectl set-hostname gip

          LANG=en_US.UTF-8
          growpart /dev/sda 2
          pvresize /dev/sda2
          lvextend "-l" "+100%FREE" "/dev/sda2"
          xfs_growfs "/dev/sda2"
          resize2fs "/dev/sda2"

          pvcreate /dev/sdb
          vgcreate gipb /dev/sdb
          lvcreate --name datab -l 100%FREE gipb
          partprobe
          mkfs.ext4 /dev/gipb/datab
          mkdir -p /mnt/mnt1
          mount /dev/gipb/datab /mnt/mnt1

          pvcreate /dev/sdc
          vgcreate gipc /dev/sdc
          lvcreate --name datac -l 100%FREE gipc
          partprobe
          mkfs.ext4 /dev/gipc/datac
          mkdir -p /mnt/mnt2
          mount /dev/gipc/datac /mnt/mnt2

          pvcreate /dev/sdd
          vgcreate gipd /dev/sdd
          lvcreate --name datad -l 100%FREE gipd
          partprobe
          mkfs.ext4 /dev/gipd/datad
          mkdir -p /mnt/mnt3
          mount /dev/gipd/datad /mnt/mnt3

          touch "$file"
        fi
```
> `/var/lib/gip/gip.sh`可忽略


1. 添加可执行权限
```bash
chmod +x /etc/rc.d/rc.local
```
1. 启动服务
```bash
/root/sh/realserver.sh start
```

## 配置Director Server



| ip              | 功能                       | 备注                      |
| :-------------- | :------------------------- | :------------------------ |
| 192.168.247.136 | LVS+Keepalived负载均衡机器 | Director Server           |
| 192.168.247.137 | LVS+Keepalived负载均衡机器 | Director Server           |
| 192.168.247.200 | VIP，不占用机器            | 挂在两台Director Server上 |


### 关闭防火墙和SELINUX
```bash
systemctl status firewalld
systemctl stop firewalld
systemctl disable firewalld
```

### 关闭SELINUX
> vi /etc/selinux/config  #修改 SELINUX=disabled



### 手动测试添加和删除VIP，以确认可以正常操作

1. 手动添加VIP
```bash
/sbin/ip addr add 192.168.247.200/24 dev eth0
```
2. 手动删除VIP
```bash
/sbin/ip addr del 192.168.247.200/24 dev eth0
```



3. 安装LVS+Keepalived
```bash
yum -y install keepalived ipvsadm

cd /etc/keepalived
mv keepalived.conf keepalived.conf_bak


```

### 配置

```conf
! Configuration File for keepalived

global_defs {
   smtp_connect_timeout 30
   router_id node77                   # router_id标识本节点的字符串，通常为hostname，在一个网络应该是唯一的
}

vrrp_instance VI_1 {
    state MASTER                      # state主设为MASTER，备设为BACKUP
    interface eth0                    # 设置HA检测网络的接口
    virtual_router_id 51              # 设置虚拟路由标志，同一个vrrp_instance实例使用唯一的标志，即MASTER和BACKUP必须相同
    priority 100                      # 设置优先级，数字越大，优先级越高，在同一个vrrp_instance中，MASTER必须比BACKUP的优先级高
    advert_int 1                      # 设置MASTER和BACKUP同步检查的时间间隔：1秒
    authentication {                  # 设置验证类型和密码
        auth_type PASS                # 设置验证类型：PASS和HA
        auth_pass MASTER_BACKUP_same  # 设置密码，在同一个vrrp_instance中，MASTER和BACKUP必须相同才能通信
    }
    virtual_ipaddress {               # 设置虚拟ip，可以设置多个
        192.168.247.200
    }
}

virtual_server 192.168.247.200 3366 { # 设置虚拟服务器：指定虚拟服务器ip和端口号，端口号必须与real_server相同，否则无法使用
    delay_loop 6                      # 运行情况检查时间，6秒
    lb_algo rr                        # 负载均衡算法：rr表示轮询
    lb_kind DR                        # 负载均衡策略：DR、NAT、TUN
    nat_mask 255.255.255.0
    persistence_timeout 50            # 会话保持时间为50s，针对动态网页很有用
    protocol TCP                      # 转发协议：TCP和UDP

    real_server 192.168.247.133 3366 {# 指定：后端服务器ip和端口
        weight 1
        TCP_CHECK {
                connect_timeout 3
                nb_get_retry 3
                delay_before_retry 3
                connect_port 3366
        }
    }
    real_server 192.168.247.134 3366 {
        weight 1                      # 节点权重，数字越大，权值越高
        TCP_CHECK {
                connect_timeout 3     # 响应超时阈值
                nb_get_retry 3        # 重试次数
                delay_before_retry 3  # 重试间隔
                connect_port 3366
        }
    }
    real_server 192.168.247.135 3366 {
        weight 1
        TCP_CHECK {
                connect_timeout 3
                nb_get_retry 3
                delay_before_retry 3
                connect_port 3366
        }
    }

}

```

主要检查两台机器的以下配置
1. router_id标识本节点的字符串，通常为hostname，在一个网络应该是唯一的
2. state主设为MASTER，备设为BACKUP
3. interface填写正确的网卡名
4. 填写正确的virtual_ipaddress、virtual_server
5. 填写正确的real_server

### 启动 Keepalived
```bash
systemctl start keepalived
systemctl status keepalived
# 检查是否有VIP
ip addr
```
### 检查`LVS`
```bash
# --检查是否加载ip_vs内核模块
lsmod |grep ip_vs
# --查看TCP连接情况
ipvsadm -Ln
# --查看请求转发情况
ipvsadm -Lnc

```

1. 把来自同一个客户端IP的请求转发到同一个RS的持久化时间：persistence_timeout
2. 一个连接创建后处于空闲状态的超时时间：包括tcp、tcpfin、udp
```bash
# --查看超时时间
ipvsadm -Ln --timeout
# --设置超时时间，视具体情况调整
ipvsadm --set 900 120 300
```

`ipvsadm -Lnc`:
- `NONE`状态代表一个客户端IP过来的连接的模板。该连接记录的源IP为客户端IP，端口为0，前面的超时时间为持久化时间`persistence_timeout`，会逐步减小。
- `ESTABLISHED`状态代表一个建立好的连接。前面的超时时间是`tcp|tcpfin|ud`p中的tcp的值。该值表示一条TCP连接记录的空闲释放时间。每当客户端和服务端的连接中有信息交互时，该超时时间都会刷新为初始值。
- `FIN_WAIT`状态代表客户端发起了FIN断连，前面的超时时间是`tcp|tcpfin|udp`中的`tcpfin`的值。





## 测试机器

| ip              | 功能                          | 备注     |
| :-------------- | :---------------------------- | :------- |
| 192.168.247.166 | 前置机，测试通过VIP访问数据库 | 测试机器 |

客户端检测
```bash
mysql -h192.168.247.200 -P3366 -uroot -proot -e "select * from information_schema.mysql;"
```
结果和在后端节点查看的一致


当一个TCP连接建立之后，启用`TCP Keepalive`的一端(本文档中的Real Server)便会启动一个计时器，如果在两小时内(默认时长)没有任何的动作，则服务器就向客户端发一个TCP探测包，用于探测客户端是否存活。
三个重要参数：
- `tcp_keepalive_time`：在TCP保活打开的情况下，最后一次数据交换到TCP发送第一个保活探测包的间隔，即允许的持续空闲时长，或者说每次正常发送心跳的周期，默认值为`7200s`
- `tcp_keepalive_probes`：在`tcp_keepalive_time`之后，没有接收到对方确认，继续发送保活探测包次数，默认值为`9`(次)
- `tcp_keepalive_intvl`：在`tcp_keepalive_time`之后，没有接收到对方确认，继续发送保活探测包的发送频率，默认值为`75s`


查看当前配置
```bash
[root@node77 keepalived]# sysctl -a | grep keepalive
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_keepalive_time = 120
```
修改参数取值：`vi /etc/sysctl.conf`
```ini
net.ipv4.tcp_keepalive_intvl=75
net.ipv4.tcp_keepalive_probes=9
net.ipv4.tcp_keepalive_time=7200
```

添加上面的配置后执行 `sysctl -p` 使其生效
1. 空闲时间超过lvs tcp时间，lvs会从请求转发列表移除对应记录，但对应的TCP连接依旧存在，此时属于半打开连接状态。
2. 空闲时间超过tcp_keepalive_time(默认2小时)，每隔tcp_keepalive_intvl(默认75秒)发送探测包，最多连续tcp_keepalive_probes(默认9次)。如果客户端的TCP响应正常，服务端将tcp_keepalive_time复位；如果客户端一直没有响应，就认为客户主机已经关闭并终止连接。
3. 空闲时间超过wait_timeout参数，数据库端直接中断连接

其中情况2、3在数据库的错误日志可以看到如下信息：
```sql
[Note] [MY-013417] [Server] The wait_timeout period was exceeded, the idle time since last command was too long.
[Note] [MY-010914] [Server] Aborted connection 6844 to db: 'unconnected' user: 'greatdb' host: '192.168.247.166' (Got timeout reading communication packets).
```
建议：wait_timeout > lvs tcp时间 > tcp_keepalive_time+tcp_keepalive_intvl*tcp_keepalive_probes

如何判断连接是否启用tcp keepalive
```bash
ss -aoen|grep ESTAB |grep 3316
```
连接信息中包含timer:(keepalive,41sec,0) 等关键字表示开启了tcp keepalive

```bash
tcpdump -nn -i eth0 host 192.168.247.166 and port 46868 -e
```
如果客户端正常它会进行回应，图中红框是关闭客户端网卡，服务端发送的的探测包一直没有收到回应，timer中的无效TCP链接探测次数会增大。


## 测试故障
为了方便演示，测试期间将Keepalived配置文件中的persistence_timeout的值由50修改为0，表示不作持久化。但是同一客户端(IP+PORT)的请求会调度给同一个Real Server，直到该连接达到了TCP空闲连接超时时间后，IPVS才会重新调度该客户端的请求。修改Keepalived配置后，重新reload配置。
```bash
systemctl reload keepalived
```

### Director Server故障
测试前确保Real Server的数据库和Director Server的Keepalived运行正常


#### 一、停止Master的Keepalived实例
检查VIP在哪一台主机上，然后停掉VIP所在的Keepalived，查看VIP是否切换
| ip              | 功能                       | 备注                      |
| :-------------- | :------------------------- | :------------------------ |
| 192.168.247.136 | LVS+Keepalived负载均衡机器 | Director Server           |
| 192.168.247.137 | LVS+Keepalived负载均衡机器 | Director Server           |
| 192.168.247.200 | VIP，不占用机器            | 挂在两台Director Server上 |
1. 检查VIP
> 确定vip在136还是137
```bash
ip addr
```
2. 停止keepalive
```bash
systemctl stop keepalived
ps aux |grep keepalived
```
3. 查看VIP及日志
```bash
ip addr
tail -100 /var/log/messages |grep -i keepalived
```
4. 在另一个节点上查看vip
```bash
ip addr
```

5. 访问数据库

在测试机使用VIP访问数据库
```bash
mysql -h192.168.247.200 -P3366 -uroot-proot -e "select * from information_schema.mysql;"
```
VIP访问是否正常

6. 测试正常后恢复keepalive

```bash
systemctl start keepalived
ip addr
tail -100 /var/log/messages |grep -i keepalived
```
- 在MASTER->BACKUP模式下，136上的Keepalived启动后先进入MASTER状态，然后对比原主和新主的优先级。发现原主的优先级高于新主的优先级，成功抢占VIP。
- 137收到高优先级的广播，退居BACKUP状态，移除VIP。回到最初的状态

7. 访问数据库

在测试机使用VIP访问数据库
```bash
mysql -h192.168.247.200 -P3366 -uroot-proot -e "select * from information_schema.mysql;"
```
VIP访问是否正常



#### 二、停止Master的Keepalived机器

线上需和客户核实能否进行关机操作

1. master上操作：
检查VIP在哪一台主机上，然后关闭VIP所在的主机，查看VIP是否切换
```bash
ip addr
shutdown -h now
```
2. 另外一个节点执行
```bash
ip addr
tail -100 /var/log/messages |grep -i keepalived
```

3. 访问数据库

在测试机使用VIP访问数据库
```bash
mysql -h192.168.247.200 -P3366 -uroot-proot -e "select * from information_schema.mysql;"
```
VIP访问是否正常

4. 开启master机器
```bash
systemctl start keepalived
ip addr
tail -100 /var/log/messages |grep -i keepalived
```

- 在MASTER->BACKUP模式下，136上的Keepalived启动后先进入MASTER状态，然后对比原主和新主的优先级。发现原主的优先级高于新主的优先级，成功抢占VIP。
- 137收到高优先级的广播，退居BACKUP状态，移除VIP。回到最初的状态。

5. 访问数据库

在测试机使用VIP访问数据库
```bash
mysql -h192.168.247.200 -P3366 -uroot-proot -e "select * from information_schema.mysql;"
```
VIP访问是否正常


### Real Server故障
测试前确保Real Server的数据库和Director Server的Keepalived运行正常

#### 一、停止RS的数据库实例
> 这里停止135上的数据库实例
1. 在测试机使用VIP每隔1秒访问数据库
```bash
for i in `seq 1 1000`;do sleep 1; mysql -h192.168.247.200 -P3366 -uroot -proot -e "select $i id,now(),@@server_id"; done
```
语句轮询从三台Real Server获取数据

2. 停止一个数据库实例

3. 查看日志
> keepalive节点
```bash
tail -100 /var/log/messages |grep -i keepalived
```
主备Keepalived将`192.168.247.135:3366`从虚拟服务中删除

停止数据库服务期间，发往到135上的连接短暂报错。故障Real Server从虚拟服务移除后，语句轮询从剩余的两台Real Server获取数据。
```sql
ERROR 2003 (HY000): can't connect to MySQL server on '192.168.247.200'
```
4. 恢复

- 在测试机使用VIP每隔1秒访问数据库
```bash
for i in `seq 1 1000`;do sleep 1; mysql -h192.168.247.200 -P3366 -uroot -proot -e "select $i id,now(),@@server_id"; done
```
- 启动数据库服务，并查看进程是否存在

- 查看日志
> keepalive节点
```bash
tail -100 /var/log/messages |grep -i keepalived
```
- 主备Keepalived将192.168.247.135:3316添加到虚拟服务
- 启动数据库服务后，192.168.247.135:3316添加到虚拟服务，语句轮询从两台调整为三台Real Server获取数据。

#### 一、停止RS的数据库机器
> 这里停止135上的数据库
1. 在测试机使用VIP每隔1秒访问数据库
```bash
for i in `seq 1 1000`;do sleep 1; mysql -h192.168.247.200 -P3366 -uroot -proot -e "select $i id,now(),@@server_id"; done
```
语句轮询从三台Real Server获取数据

2. 关闭机器

```bash
shutdown -h now
```
对于GreatDBCluster5.0集群计算节点和数据节点都是MGR，各复制组内挂一个数据库实例不影响集群正常运行，shard2需重新选Primary。

3. 查看日志
> keepalive节点
```bash
tail -100 /var/log/messages |grep -i keepalived
```
主备Keepalived将192.168.247.135:3366从虚拟服务中删除

关闭RS主机期间，发往到135上的连接短暂报错。故障Real Server从虚拟服务移除后，语句轮询从剩余的两台Real Server获取数据。
```sql
ERROR 2003 (HY000): can't connect to MySQL server on '192.168.247.200'
```
4. 恢复

- 在测试机使用VIP每隔1秒访问数据库
```bash
for i in `seq 1 1000`;do sleep 1; mysql -h192.168.247.200 -P3366 -uroot -proot -e "select $i id,now(),@@server_id"; done
```
- 启动数据库机器，并查看进程是否存在

- 查看日志
> keepalive节点
```bash
tail -100 /var/log/messages |grep -i keepalived
```
- 主备Keepalived将192.168.247.135:3316添加到虚拟服务
- 启动数据库服务后，192.168.247.135:3316添加到虚拟服务，语句轮询从两台调整为三台Real Server获取数据。



## 部署仲裁(按需)
> 仲裁脚本建议部署于Keepalived以外的第三台机器。




















