---
title: module
description: module
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
- [module](https://docs.ansible.com/ansible/latest/collections/index_module.html)
{{< /alert >}}

{{< alert color="secondary" >}}


{{< /alert >}}




























### setup
- "ansible_distribution": "CentOS",
- "ansible_distribution_file_parsed": true,
- "ansible_distribution_file_path": "/etc/redhat-release",
- "ansible_distribution_file_variety": "RedHat",
- "ansible_distribution_major_version": "7",
- "ansible_distribution_release": "Core",
- "ansible_distribution_version": "7.6",


`ansible localhost -m setup`
```bash
root@node70:~# ansible localhost -m setup
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.17.134.70",
            "172.18.0.1",
            "192.168.122.1"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::88a0:34ff:feda:deeb",
            "fe80::7811:7aff:fede:b345",
            "fe80::2052:d7ff:fe29:6c48",
            "fe80::1435:bfff:fe0d:eb98",
            "fe80::c89:bff:fe7f:c311",
            "fe80::e8b2:43ff:fe20:3877",
            "fe80::216:3eff:feeb:f9aa",
            "fe80::42:f1ff:fee2:92b8",
            "fe80::1cb7:b5ff:fe80:f5f8",
            "fe80::c454:99ff:fef9:57f",
            "fe80::f8f1:27ff:fe8e:c75e"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "01/01/2011",
        "ansible_bios_version": "0.5.1",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-3.10.0-957.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "crashkernel": "auto",
            "quiet": true,
            "rhgb": true,
            "ro": true,
            "root": "UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
        },
        "ansible_date_time": {
            "date": "2024-03-04",
            "day": "04",
            "epoch": "1709551262",
            "hour": "19",
            "iso8601": "2024-03-04T11:21:02Z",
            "iso8601_basic": "20240304T192102716584",
            "iso8601_basic_short": "20240304T192102",
            "iso8601_micro": "2024-03-04T11:21:02.716584Z",
            "minute": "21",
            "month": "03",
            "second": "02",
            "time": "19:21:02",
            "tz": "CST",
            "tz_offset": "+0800",
            "weekday": "Monday",
            "weekday_number": "1",
            "weeknumber": "10",
            "year": "2024"
        },
        "ansible_default_ipv4": {
            "address": "172.17.134.70",
            "alias": "eth0",
            "broadcast": "172.17.143.255",
            "gateway": "172.17.130.1",
            "interface": "eth0",
            "macaddress": "00:16:3e:eb:f9:aa",
            "mtu": 1500,
            "netmask": "255.255.240.0",
            "network": "172.17.128.0",
            "type": "ether"
        },
        "ansible_default_ipv6": {},
        "ansible_device_links": {
            "ids": {
                "sda": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                ],
                "sda1": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                ],
                "sda2": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                ],
                "sr0": [
                    "ata-QEMU_DVD-ROM_QM00001"
                ],
                "sr1": [
                    "ata-QEMU_DVD-ROM_QM00002"
                ]
            },
            "labels": {
                "sr1": [
                    "cidata"
                ]
            },
            "masters": {},
            "uuids": {
                "sda1": [
                    "b4a1030d-3f54-4598-b2fc-627c781f4105"
                ],
                "sda2": [
                    "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                ],
                "sr1": [
                    "2023-10-09-12-01-19-00"
                ]
            }
        },
        "ansible_devices": {
            "sda": {
                "holders": [],
                "host": "SCSI storage controller: Red Hat, Inc. Virtio SCSI",
                "links": {
                    "ids": [
                        "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "QEMU HARDDISK",
                "partitions": {
                    "sda1": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "b4a1030d-3f54-4598-b2fc-627c781f4105"
                            ]
                        },
                        "sectors": "614400",
                        "sectorsize": 512,
                        "size": "300.00 MB",
                        "start": "2048",
                        "uuid": "b4a1030d-3f54-4598-b2fc-627c781f4105"
                    },
                    "sda2": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                            ]
                        },
                        "sectors": "418813919",
                        "sectorsize": 512,
                        "size": "199.71 GB",
                        "start": "616448",
                        "uuid": "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                    }
                },
                "removable": "0",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "419430400",
                "sectorsize": "512",
                "size": "200.00 GB",
                "support_discard": "4096",
                "vendor": "QEMU",
                "virtual": 1
            },
            "sr0": {
                "holders": [],
                "host": "IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]",
                "links": {
                    "ids": [
                        "ata-QEMU_DVD-ROM_QM00001"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "QEMU DVD-ROM",
                "partitions": {},
                "removable": "1",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "2097151",
                "sectorsize": "512",
                "size": "1024.00 MB",
                "support_discard": "0",
                "vendor": "QEMU",
                "virtual": 1
            },
            "sr1": {
                "holders": [],
                "host": "IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]",
                "links": {
                    "ids": [
                        "ata-QEMU_DVD-ROM_QM00002"
                    ],
                    "labels": [
                        "cidata"
                    ],
                    "masters": [],
                    "uuids": [
                        "2023-10-09-12-01-19-00"
                    ]
                },
                "model": "QEMU DVD-ROM",
                "partitions": {},
                "removable": "1",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "724",
                "sectorsize": "2048",
                "size": "362.00 KB",
                "support_discard": "0",
                "vendor": "QEMU",
                "virtual": 1
            }
        },
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.6",
        "ansible_dns": {
            "nameservers": [
                "172.17.141.251",
                "223.5.5.5",
                "114.114.114.114"
            ]
        },
        "ansible_docker0": {
            "active": true,
            "device": "docker0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [requested on]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "on",
                "tx_gso_robust": "off [requested on]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "id": "8000.0242f1e292b8",
            "interfaces": [
                "veth91dc4a8",
                "vethc30a029",
                "vethd0f4614",
                "veth6ffafa8",
                "vethd84c75c",
                "veth0d6a563",
                "veth043ffcf",
                "veth2fa44d1",
                "veth2fdd7b3"
            ],
            "ipv4": {
                "address": "172.18.0.1",
                "broadcast": "172.18.255.255",
                "netmask": "255.255.0.0",
                "network": "172.18.0.0"
            },
            "ipv6": [
                {
                    "address": "fe80::42:f1ff:fee2:92b8",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "02:42:f1:e2:92:b8",
            "mtu": 1500,
            "promisc": false,
            "stp": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "bridge"
        },
        "ansible_domain": "",
        "ansible_effective_group_id": 0,
        "ansible_effective_user_id": 0,
        "ansible_env": {
            "ACE_ROOT": "/root/local/ACE_wrappers",
            "HISTCONTROL": "ignoredups",
            "HISTSIZE": "1000",
            "HOME": "/root",
            "HOSTNAME": "node70",
            "LANG": "en_US.UTF-8",
            "LD_LIBARY_PATH": "/root/local/ACE_wrappers/lib:",
            "LD_LIBRARY_PATH": "/root/local/bison/lib",
            "LESSOPEN": "||/usr/bin/lesspipe.sh %s",
            "LLVM_HOME": "/opt/rh/llvm-toolset-7.0/root/usr",
            "LOGNAME": "root",
            "LS_COLORS": "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:",
            "MAIL": "/var/spool/mail/root",
            "PATH": "/root/local/bison/bin:/root/local/bison/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/opt/rh/llvm-toolset-7.0/root/usr/bin:/root/local/ACE_wrappers:/root/bin:/opt/rh/llvm-toolset-7.0/root/usr/bin",
            "PWD": "/root",
            "SELINUX_LEVEL_REQUESTED": "",
            "SELINUX_ROLE_REQUESTED": "",
            "SELINUX_USE_CURRENT_RANGE": "",
            "SHELL": "/bin/bash",
            "SHLVL": "3",
            "SSH_CLIENT": "172.17.120.253 62976 22",
            "SSH_CONNECTION": "172.17.120.253 62976 172.17.134.70 22",
            "SSH_TTY": "/dev/pts/0",
            "TERM": "xterm",
            "USER": "root",
            "XDG_DATA_DIRS": "/root/.local/share/flatpak/exports/share:/var/lib/flatpak/exports/share:/usr/local/share:/usr/share",
            "XDG_RUNTIME_DIR": "/run/user/0",
            "XDG_SESSION_ID": "13833",
            "_": "/usr/bin/python2"
        },
        "ansible_eth0": {
            "active": true,
            "device": "eth0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "172.17.134.70",
                "broadcast": "172.17.143.255",
                "netmask": "255.255.240.0",
                "network": "172.17.128.0"
            },
            "ipv6": [
                {
                    "address": "fe80::216:3eff:feeb:f9aa",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "00:16:3e:eb:f9:aa",
            "module": "virtio_net",
            "mtu": 1500,
            "pciid": "virtio0",
            "promisc": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_fibre_channel_wwn": [],
        "ansible_fips": false,
        "ansible_form_factor": "Other",
        "ansible_fqdn": "node70",
        "ansible_hostname": "node70",
        "ansible_hostnqn": "",
        "ansible_interfaces": [
            "eth0",
            "veth91dc4a8",
            "docker0",
            "veth6ffafa8",
            "vethd84c75c",
            "lo",
            "veth043ffcf",
            "vethd0f4614",
            "veth2fa44d1",
            "vethc30a029",
            "veth0d6a563",
            "veth2fdd7b3",
            "virbr0-nic",
            "virbr0"
        ],
        "ansible_is_chroot": false,
        "ansible_iscsi_iqn": "iqn.1994-05.com.redhat:beef1c944ea1",
        "ansible_kernel": "3.10.0-957.el7.x86_64",
        "ansible_kernel_version": "#1 SMP Thu Nov 8 23:39:32 UTC 2018",
        "ansible_lo": {
            "active": true,
            "device": "lo",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "on [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on [fixed]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off [fixed]",
                "tx_scatter_gather": "on [fixed]",
                "tx_scatter_gather_fraglist": "on [fixed]",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "on [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "127.0.0.1",
                "broadcast": "",
                "netmask": "255.0.0.0",
                "network": "127.0.0.0"
            },
            "ipv6": [
                {
                    "address": "::1",
                    "prefix": "128",
                    "scope": "host"
                }
            ],
            "mtu": 65536,
            "promisc": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "loopback"
        },
        "ansible_local": {},
        "ansible_lsb": {},
        "ansible_lvm": {
            "lvs": {},
            "pvs": {},
            "vgs": {}
        },
        "ansible_machine": "x86_64",
        "ansible_machine_id": "ebbb68ebb9fa496d97a89e45ebf87933",
        "ansible_memfree_mb": 261,
        "ansible_memory_mb": {
            "nocache": {
                "free": 3921,
                "used": 3900
            },
            "real": {
                "free": 261,
                "total": 7821,
                "used": 7560
            },
            "swap": {
                "cached": 0,
                "free": 0,
                "total": 0,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 7821,
        "ansible_mounts": [
            {
                "block_available": 35379,
                "block_size": 4096,
                "block_total": 75945,
                "block_used": 40566,
                "device": "/dev/sda1",
                "fstype": "xfs",
                "inode_available": 153215,
                "inode_total": 153600,
                "inode_used": 385,
                "mount": "/boot",
                "options": "rw,seclabel,relatime,attr2,inode64,noquota",
                "size_available": 144912384,
                "size_total": 311070720,
                "uuid": "b4a1030d-3f54-4598-b2fc-627c781f4105"
            },
            {
                "block_available": 39657908,
                "block_size": 4096,
                "block_total": 51498807,
                "block_used": 11840899,
                "device": "/dev/sda2",
                "fstype": "ext4",
                "inode_available": 12640192,
                "inode_total": 13065248,
                "inode_used": 425056,
                "mount": "/",
                "options": "rw,seclabel,relatime,data=ordered",
                "size_available": 162438791168,
                "size_total": 210939113472,
                "uuid": "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
            }
        ],
        "ansible_nodename": "node70",
        "ansible_os_family": "RedHat",
        "ansible_pkg_mgr": "yum",
        "ansible_proc_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-3.10.0-957.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "crashkernel": "auto",
            "quiet": true,
            "rhgb": true,
            "ro": true,
            "root": "UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
        },
        "ansible_processor": [
            "0",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "1",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "2",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "3",
            "GenuineIntel",
            "Intel Core Processor (Haswell)"
        ],
        "ansible_processor_cores": 2,
        "ansible_processor_count": 1,
        "ansible_processor_threads_per_core": 2,
        "ansible_processor_vcpus": 4,
        "ansible_product_name": "Standard PC (i440FX + PIIX, 1996)",
        "ansible_product_serial": "NA",
        "ansible_product_uuid": "95C8753F-BAE3-47A2-89F1-DBA0EF255D74",
        "ansible_product_version": "pc-i440fx-2.0",
        "ansible_python": {
            "executable": "/usr/bin/python2",
            "has_sslcontext": true,
            "type": "CPython",
            "version": {
                "major": 2,
                "micro": 5,
                "minor": 7,
                "releaselevel": "final",
                "serial": 0
            },
            "version_info": [
                2,
                7,
                5,
                "final",
                0
            ]
        },
        "ansible_python_version": "2.7.5",
        "ansible_real_group_id": 0,
        "ansible_real_user_id": 0,
        "ansible_selinux": {
            "config_mode": "enforcing",
            "mode": "enforcing",
            "policyvers": 31,
            "status": "enabled",
            "type": "targeted"
        },
        "ansible_selinux_python_present": true,
        "ansible_service_mgr": "systemd",
        "ansible_ssh_host_key_ecdsa_public": "AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEIGpUTzYRY8NM7smZTUQXPO0d2dHgwPgc26s8OxJD9oztXtZAFndSE0DS2/apuw5kBAgSlDm6u+uQ8EocZb01A=",
        "ansible_ssh_host_key_ed25519_public": "AAAAC3NzaC1lZDI1NTE5AAAAIAuVOpE0WP5+fobd1YiY3U/RZCuvHcsB8S/gnp+Z61se",
        "ansible_ssh_host_key_rsa_public": "AAAAB3NzaC1yc2EAAAADAQABAAABAQDTxYlGhVIyZlp/aRZGNzoYfP4JG58dftqE2KyQ1oPk/kbY4YsBzbXu0tFGFyafqAWY14tt/LM/LXJuNmSGzUf9wcSypkaiwtoxJOAyLoNqy2srXBdvKW5aUwmPLoqmsXgXyANFbfsynlNyQHViv96t0YIJmbi6KFZIvGoTB3SuMXNbmk2v7eVp7ghdlY1RSRWrepOUbgerG6ht8cQ/cr/LyBVIAnF7piyMtRTmmiEy7EtCfZiMt+0xEXB2Nbowvhm0zpTXdH1mRPIvEafrXNca6FboPmZrrORqxPEdRMJ2GBUtmIXFBSDNS5ftFNUP4p7sQcGnLOioftAGoKG1SFph",
        "ansible_swapfree_mb": 0,
        "ansible_swaptotal_mb": 0,
        "ansible_system": "Linux",
        "ansible_system_capabilities": [
            "cap_chown",
            "cap_dac_override",
            "cap_dac_read_search",
            "cap_fowner",
            "cap_fsetid",
            "cap_kill",
            "cap_setgid",
            "cap_setuid",
            "cap_setpcap",
            "cap_linux_immutable",
            "cap_net_bind_service",
            "cap_net_broadcast",
            "cap_net_admin",
            "cap_net_raw",
            "cap_ipc_lock",
            "cap_ipc_owner",
            "cap_sys_module",
            "cap_sys_rawio",
            "cap_sys_chroot",
            "cap_sys_ptrace",
            "cap_sys_pacct",
            "cap_sys_admin",
            "cap_sys_boot",
            "cap_sys_nice",
            "cap_sys_resource",
            "cap_sys_time",
            "cap_sys_tty_config",
            "cap_mknod",
            "cap_lease",
            "cap_audit_write",
            "cap_audit_control",
            "cap_setfcap",
            "cap_mac_override",
            "cap_mac_admin",
            "cap_syslog",
            "35",
            "36+ep"
        ],
        "ansible_system_capabilities_enforced": "True",
        "ansible_system_vendor": "QEMU",
        "ansible_uptime_seconds": 4090257,
        "ansible_user_dir": "/root",
        "ansible_user_gecos": "root",
        "ansible_user_gid": 0,
        "ansible_user_id": "root",
        "ansible_user_shell": "/bin/bash",
        "ansible_user_uid": 0,
        "ansible_userspace_architecture": "x86_64",
        "ansible_userspace_bits": "64",
        "ansible_veth043ffcf": {
            "active": true,
            "device": "veth043ffcf",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::c454:99ff:fef9:57f",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "c6:54:99:f9:05:7f",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth0d6a563": {
            "active": true,
            "device": "veth0d6a563",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::88a0:34ff:feda:deeb",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "8a:a0:34:da:de:eb",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth2fa44d1": {
            "active": true,
            "device": "veth2fa44d1",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::f8f1:27ff:fe8e:c75e",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "fa:f1:27:8e:c7:5e",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth2fdd7b3": {
            "active": true,
            "device": "veth2fdd7b3",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::1cb7:b5ff:fe80:f5f8",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "1e:b7:b5:80:f5:f8",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth6ffafa8": {
            "active": true,
            "device": "veth6ffafa8",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::2052:d7ff:fe29:6c48",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "22:52:d7:29:6c:48",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth91dc4a8": {
            "active": true,
            "device": "veth91dc4a8",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::e8b2:43ff:fe20:3877",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "ea:b2:43:20:38:77",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_vethc30a029": {
            "active": true,
            "device": "vethc30a029",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::c89:bff:fe7f:c311",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "0e:89:0b:7f:c3:11",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_vethd0f4614": {
            "active": true,
            "device": "vethd0f4614",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::1435:bfff:fe0d:eb98",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "16:35:bf:0d:eb:98",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_vethd84c75c": {
            "active": true,
            "device": "vethd84c75c",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::7811:7aff:fede:b345",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "7a:11:7a:de:b3:45",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_virbr0": {
            "active": false,
            "device": "virbr0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [requested on]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [requested on]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "on",
                "tx_gso_robust": "off [requested on]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "off [requested on]",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "off [requested on]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "id": "8000.52540096cfdd",
            "interfaces": [
                "virbr0-nic"
            ],
            "ipv4": {
                "address": "192.168.122.1",
                "broadcast": "192.168.122.255",
                "netmask": "255.255.255.0",
                "network": "192.168.122.0"
            },
            "macaddress": "52:54:00:96:cf:dd",
            "mtu": 1500,
            "promisc": false,
            "stp": true,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "bridge"
        },
        "ansible_virbr0_nic": {
            "active": false,
            "device": "virbr0-nic",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "off",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "off [requested on]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "off",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "off [requested on]",
                "tx_tcp_ecn_segmentation": "off [requested on]",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "off [requested on]",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "off [requested on]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "macaddress": "52:54:00:96:cf:dd",
            "mtu": 1500,
            "promisc": true,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_type": "kvm",
        "gather_subset": [
            "all"
        ],
        "module_setup": true
    },
    "changed": false
}

```


