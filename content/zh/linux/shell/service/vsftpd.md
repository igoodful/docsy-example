---
title: vsftpd
description: vsftpd
date: 2023-11-08
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

---
&#128311;【】2017年上半年，城镇居民人均可支配收入18332元，增长8.1%（以下如无特别说明，均为同比名义增长）

2016年上半年，城镇居民人均可支配收入约为多少元？

【解析】$\cfrac{18332}{ 1 + 0.081 } = 17000$


### 部署


```bash
ftp_user=myftp
ftp_datadir="/data/${myftp}"
mkdir -p $ftp_datadir
useradd $ftp_user
chown -R  $ftp_user:$ftp_user $ftp_datadir
echo "$ftp_user" | passwd --stdin $ftp_user

yum install vsftpd -y
yum install ftp -y
rpm -qa | grep vsftpd
systemctl restart vsftpd
systemctl enable vsftpd
systemctl status vsftpd
cat > /etc/vsftpd/vsftpd.conf <<EOF
anonymous_enable=NO　　　　　　    # 不允许匿名用户登录
local_enable=YES　　　　　　　　   # 允许本地用户登录
write_enable=YES　　　　　　　　   # 允许写入
local_umask=022　　　　            # 掩码为022，表示755的权限
dirmessage_enable=YES　　　　       # 如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启。
xferlog_enable=YES　　　　                #是否启用上传/下载日志记录。如果启用，则上传与下载的信息将被完整纪录在xferlog_file 所定义的档案中。预设为开启。
connect_from_port_20=YES　　  　　# 指定FTP使用20端口进行数据传输，默认值为YES。
xferlog_std_format=YES　　　          　# 如果启用，则日志文件将会写成xferlog的标准格式，如同wu-ftpd 一般。默认值为关闭
listen=YES　　　　                                  # 监听IPv4 sockets
listen_ipv6=NO　　　                          　# 关闭ipv6
pam_service_name=vsftpd　　　　           # 设置PAM使用的名称，默认值为/etc/pam.d/vsftpd。
userlist_enable=YES　　　　                # 是否启用vsftpd.user_list文件
tcp_wrappers=YES　　　　                   # 如果启用，则vsftpd服务器会检查/etc/hosts.allow 和/etc/hosts.deny 中的设置，来决定请求连接的主机，是否允许访问该FTP服务器
use_localtime=YES　　　　                  # vsftpd将显示当前时区中包含时间的目录列表
port_enable=YES　　　                     　# 如果为NO，则表示禁止用PORT方法获取数据连接
chroot_local_user=YES　　　　　　            # 全部用户被限制在主目录
chroot_list_enable=YES　　　　　　          # 启用例外用户名单
chroot_list_file=/etc/vsftpd/chroot_list   # 指定例外用户列表文件，列表中的用户不被锁定在主目录
allow_writeable_chroot=YES　　　　          # 允许写入
local_root=${ftp_datadir}           # 设置本地用户登录后所在的目录
EOF

```





