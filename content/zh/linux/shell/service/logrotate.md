---
title: logrotate
description: logrotate
date: 2023-11-08
weight: 30000
---






### 源码编译

绝大部分 Linux 发行版都有内置日志滚动切割工具logrotate，使用它可以方便地设置日志滚动切割规则，并且可以自动清理过期的日志文件。

logrotate的配置文件：

/etc/logrotate.conf 主配置文件
/etc/logrotate.d 目录下可以存放很多具体的日志滚动切割规则配置文件
当我们需要设置日志滚动切割规则的时候，可以在/etc/logrotate.d下新建一个配置文件。例如/etc/logrotate.d/nginx，这个文件的内容如下：
```sql
/var/log/nginx/*.log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    create 640 root root
    sharedscripts
    postrotate
        [ -f /var/run/nginx.pid ] && kill -USR1 \`cat /var/run/nginx.pid\`
    endscript
}
```
这个配置文件的含义是：

daily 每天滚动一次

missingok 如果文件不存在，则不滚动

rotate 7 保留最近7个日志文件

compress 压缩日志文件

delaycompress 延迟压缩

notifempty 如果文件为空，则不滚动

create 640 root root 新建日志文件的属主及权限，如果 nginx 不是root用户运行的要特别注意

sharedscripts 共享脚本，即日志滚动完成后再运行脚本，否则每滚动一个日志文件都要运行一次脚本

postrotate 日志滚动完成后运行的脚本，有些业务日志可以不需要这个脚本

当设置好日志滚动切割规则配置后，可以使用logrotate -d来验证规则，例如：

如果希望马上看到效果，可以使用logrotate -f来强制进行滚动切割，例如：

logrotate -f /etc/logrotate.d/nginx

其它参数

compress 压缩滚动之后的历史日志

nocompress 不压缩滚动之后的历史日志

copytruncate 用于还在打开中的日志文件，把当前日志备份并截断；是先拷贝再清空的方式，拷贝和清空之间有一个时间差，可能会丢失部分日志数据。

nocopytruncate 备份日志文件不过不截断

create mode owner group 指定创建新文件的属主和权限

nocreate 不建立新的日志文件

delaycompress 和 compress 一起使用时，历史日志文件到下一次滚动时才压缩

nodelaycompress 覆盖 delaycompress 选项，滚动同时压缩

missingok 如果日志缺失，不报错继续滚动下一个日志

errors address 滚动时的错误信息发送到指定的 Email 地址

ifempty 即使日志文件为空文件也做滚动

notifempty 当日志文件为空时，不进行滚动

mail address 把滚动的日志文件发送到指定的 Email 地址

nomail 滚动时不发送日志文件

olddir directory 滚动后的日志文件放入指定的目录，必须和当前日志文件在同一个文件系统

noolddir 滚动后的日志文件和当前日志文件放在同一个目录下

sharedscripts 共享脚本，即日志滚动完成后再运行脚本，否则每滚动一个日志文件都要运行一次脚本

prerotate 在滚动之前需要执行的指令，例如修改文件的属性等动作；必须独立成行

postrotate 在滚动之后需要执行的指令，例如重新启动 (kill -HUP) 某个服务；必须独立成行

daily 指定滚动周期为每天

weekly 指定滚动周期为每周

monthly 指定滚动周期为每月

rotate count 指定日志文件删除之前滚动的次数，0指不保留备份，5指保留 5 个备份

dateext 使用当期日期作为命名格式

dateformat .%s 配合dateext使用，紧跟在下一行出现，定义文件切割后的文件名，必须配合dateext使用，只支持%Y/%m/%d/%s四个参数

size log-size(或minsize log-size) 当日志文件到达指定的大小时才滚动，以下为合法格式：

size = 5或size 5 （>= 5 个字节就滚动）

size = 100k或size 100k

size = 100M或size 100M
{{<alert>}}


{{</alert>}}












