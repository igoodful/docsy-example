---
title: grub2-tools
description: grub2-tools
date: 2025-03-22
weight: 102
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->

- https://sourceforge.net/p/acl/
- https://github.com/acl-dev/acl/


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "grub2-tools" {
node [
        fontsize = "12"
];
    rankdir="LR";
    "grub2-tools" [ label = "libacl", shape = octagon ];
    "grub2-mkconfig" [ label = "grub2-mkconfig", shape = octagon ];
    "grub2-probe" [ label = "grub2-probe", shape = octagon ];
    "grub2-reboot" [ label = "grub2-reboot", shape = octagon ];
    "grub2-file" [ label = "grub2-file", shape = octagon ];
    "grub2-install" [ label = "grub2-install", shape = octagon ];
    "grub2-bios-setup" [ label = "grub2-bios-setup", shape = octagon ];

    "grub2-tools" -> "grub2-mkconfig";
    "grub2-tools" -> "grub2-probe";
    "grub2-tools" -> "grub2-reboot";
    "grub2-tools" -> "grub2-file";
    "grub2-tools" -> "grub2-install";
    "grub2-tools" -> "grub2-bios-setup";
}
```




## 3. 安装


#### 3.1 yum安装
```bash
2025-03-23T13:33:25 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qf /usr/sbin/grub2-mkconfig 
grub2-tools-2.02-0.76.el7.centos.x86_64
2025-03-23T13:56:08 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql grub2-tools-2.02-0.76.el7.centos.x86_64
/etc/default/grub
/etc/grub.d/00_header
/etc/grub.d/01_users
/etc/grub.d/10_linux
/etc/grub.d/20_linux_xen
/etc/grub.d/20_ppc_terminfo
/etc/grub.d/30_os-prober
/etc/grub.d/40_custom
/etc/grub.d/41_custom
/etc/grub.d/README
/usr/bin/grub2-file
/usr/bin/grub2-menulst2cfg
/usr/bin/grub2-mkrelpath
/usr/bin/grub2-render-label
/usr/bin/grub2-script-check
/usr/sbin/grub2-bios-setup
/usr/sbin/grub2-install
/usr/sbin/grub2-macbless
/usr/sbin/grub2-mkconfig
/usr/sbin/grub2-probe
/usr/sbin/grub2-reboot
/usr/sbin/grub2-rpm-sort
/usr/share/bash-completion/completions/grub
/usr/share/grub/ascii.pf2
/usr/share/grub/euro.pf2
/usr/share/grub/grub-mkconfig_lib
/usr/share/grub/unicode.pf2
/usr/share/info/grub2-dev.info.gz
/usr/share/info/grub2.info.gz
/usr/share/man/man1/grub2-file.1.gz
/usr/share/man/man1/grub2-menulst2cfg.1.gz
/usr/share/man/man1/grub2-mkrelpath.1.gz
/usr/share/man/man1/grub2-render-label.1.gz
/usr/share/man/man1/grub2-script-check.1.gz
/usr/share/man/man8/grub2-bios-setup.8.gz
/usr/share/man/man8/grub2-macbless.8.gz
/usr/share/man/man8/grub2-mkconfig.8.gz
/usr/share/man/man8/grub2-probe.8.gz
/usr/share/man/man8/grub2-reboot.8.gz
/usr/share/man/man8/grub2-rpm-sort.8.gz


```




#### 3.2 源码安装

```bash


```

