---
title: 卸载adm
description: 卸载adm
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}




```sql
systemctl stop  promtail

systemctl disable promtail

systemctl stop  adm-dbinit

systemctl disable  adm-dbinit
```

### promtail.service
> /etc/systemd/system/multi-user.target.wants/promtail.service
> /usr/lib/systemd/system/promtail.service
```sql
[root@k8s-node-57 ~]# cat /usr/lib/systemd/system/promtail.service
[Unit]
Description=promtail service
After=network.service

[Service]
Type=simple
ExecStart=/bin/sh -c "/gdb/adm-dbinit/bin/promtail -config.file=/gdb/adm-dbinit/conf/promtail.yaml &>> /gdb/adm-dbinit/log/promtail.log"
Restart=on-failure
TimeoutStopSec=15

[Install]
WantedBy=multi-user.target

```

### adm-dbinit.service
> /etc/systemd/system/multi-user.target.wants/adm-dbinit.service
> /usr/lib/systemd/system/adm-dbinit.service
```sql
[root@k8s-node-57 ~]# cat /usr/lib/systemd/system/adm-dbinit.service
[Unit]
Description=ADM dbinit
After=network.service

[Service]
User=gdb
Group=gdb
Type=simple
WorkingDirectory=/gdb/adm-dbinit
PermissionsStartOnly=true
ExecStartPre=/usr/bin/mkdir -p /gdb/adm-dbinit/log
ExecStartPre=/usr/bin/chown gdb:gdb /gdb/adm-dbinit/log
ExecStart=/gdb/adm-dbinit/bin/dbinit -log /gdb/adm-dbinit/log/dbinit.log -port 19999 -l INFO
KillMode=process
Restart=on-failure
LimitNOFILE=1024000
TimeoutStopSec=15

[Install]


```


















