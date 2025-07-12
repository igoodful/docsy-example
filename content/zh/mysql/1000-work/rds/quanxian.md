---
title: RDS的安装包解压后不要设置 777权限
date: 2023-10-23
description: RDS-5.1.9 升级至 RDS-6.0.1


weight: 2000
---

{{< alert >}}
问题：不小心将权限全部改成777了

解决：将全部权限改成：755

{{< /alert >}}





```sql
[mysql@node1 rds-ansible]$ sudo chmod -R  777 ./

[mysql@node1 rds-ansible]$ ll
total 6416
-rwxrwxrwx.  1 mysql mysql     475 Nov 13 09:16 ansible.cfg
-rwxrwxrwx.  1 mysql mysql 1636228 Nov 13 09:51 ansible.log
-rwxrwxrwx.  1 mysql mysql     112 Nov 13 09:16 dbinit-deploy.yml
-rwxrwxrwx.  1 mysql mysql    2747 Nov 13 09:16 deploy.yml
drwxrwxrwx.  2 mysql mysql    4096 Nov 13 09:16 fact_files
-rwxrwxrwx.  1 mysql mysql     529 Nov 13 09:14 facts.yml
drwxrwxrwx.  8 mysql mysql    4096 Nov 13 09:16 files
-rwxrwxrwx.  1 mysql mysql 4873192 Nov 13 09:14 greatrds-ansible.log
drwxrwxrwx.  2 mysql mysql    4096 Nov 13 09:16 group_vars
-rwxrwxrwx.  1 mysql mysql    1941 Nov 13 09:14 inventory.yml
-rwxrwxrwx.  1 mysql mysql     214 Nov 13 09:16 pre-check.yml
-rwxrwxrwx.  1 mysql mysql    4576 Nov 13 09:16 prepare.py
drwxrwxrwx. 24 mysql mysql    4096 Nov 13 09:16 roles
drwxrwxrwx.  2 mysql mysql    4096 Nov 13 09:16 templates
-rwxrwxrwx.  1 mysql mysql    1176 Nov 13 09:14 upgrade.yml


[mysql@node1 rds-ansible]$ ansible-playbook deploy.yml -v
 [WARNING] Ansible is being run in a world writable directory (/home/mysql/RDS-5.1.9.0rc16-release-10-17-x64_redhat8/rds-ansible), ignoring it as an ansible.cfg source. For more information see https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
/home/mysql/RDS-5.1.9.0rc16-release-10-17-x64_redhat8/rds-ansible/files/ansible_install/ansible-playbook/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  from cryptography.exceptions import InvalidSignature
No config file found; using defaults
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [set facts for all hosts] ********************************************
skipping: no hosts matched

PLAY [Apply role os] ***************************************************
skipping: no hosts matched

PLAY [Apply role docker] ****************************************************
skipping: no hosts matched

PLAY [Apply role load images] ***********************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: reposerver

PLAY [Apply role repo] *****************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: pypiserver

PLAY [Apply role pypiserver] ************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: registry

PLAY [Apply role registry] **************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: database

PLAY [Apply role mysql] ****************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: monitor

PLAY [Apply role prometheus] ***********************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: rabbitmq

PLAY [Apply role rabbitmq] ************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: nginx

PLAY [Apply role nginx] *************************************************
skipping: no hosts matched

PLAY [Upload wheel package] ************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: api

PLAY [Apply role api] ***************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: conductor

PLAY [Apply role conductor] **********************************************
skipping: no hosts matched

PLAY [Apply role monitor] ************************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: auth

PLAY [Apply role auth] ***************************************
skipping: no hosts matched
[WARNING]: Could not match supplied host pattern, ignoring: exporter

PLAY [Apply role exporter] ************************************************
skipping: no hosts matched

PLAY [Checking the Installation Result And Cleaning the battlefield] *************
skipping: no hosts matched

PLAY RECAP *********************************

```









