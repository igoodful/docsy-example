---
title: postgresql
description: postgresql
date: 2023-11-06
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




## 文件

#### bin

```bash

[root@centos8-25 /glc/postgresql/app/postgresql-17.4/bin] # ll
total 65240
-rwxr-xr-x 1 root root   332048 2025-04-20T21:06:13 clusterdb
-rwxr-xr-x 1 root root   327896 2025-04-20T21:06:13 createdb
-rwxr-xr-x 1 root root   340160 2025-04-20T21:06:13 createuser
-rwxr-xr-x 1 root root   316464 2025-04-20T21:06:13 dropdb
-rwxr-xr-x 1 root root   316048 2025-04-20T21:06:13 dropuser
-rwxr-xr-x 1 root root  2095024 2025-04-20T21:06:11 ecpg
-rwxr-xr-x 1 root root   588304 2025-04-20T21:06:11 initdb
-rwxr-xr-x 1 root root   418352 2025-04-20T21:06:12 pg_amcheck
-rwxr-xr-x 1 root root   171032 2025-04-20T21:06:12 pg_archivecleanup
-rwxr-xr-x 1 root root   645400 2025-04-20T21:06:12 pg_basebackup
-rwxr-xr-x 1 root root   700312 2025-04-20T21:06:13 pgbench
-rwxr-xr-x 1 root root   255072 2025-04-20T21:06:12 pg_checksums
-rwxr-xr-x 1 root root   550744 2025-04-20T21:06:12 pg_combinebackup
-rwxr-xr-x 1 root root   163544 2025-04-20T21:06:12 pg_config
-rwxr-xr-x 1 root root   203776 2025-04-20T21:06:12 pg_controldata
-rwxr-xr-x 1 root root   410696 2025-04-20T21:06:12 pg_createsubscriber
-rwxr-xr-x 1 root root   250696 2025-04-20T21:06:12 pg_ctl
-rwxr-xr-x 1 root root  1633408 2025-04-20T21:06:12 pg_dump
-rwxr-xr-x 1 root root   473240 2025-04-20T21:06:12 pg_dumpall
-rwxr-xr-x 1 root root   310824 2025-04-20T21:06:13 pg_isready
-rwxr-xr-x 1 root root   391784 2025-04-20T21:06:12 pg_receivewal
-rwxr-xr-x 1 root root   373296 2025-04-20T21:06:12 pg_recvlogical
-rwxr-xr-x 1 root root   239632 2025-04-20T21:06:12 pg_resetwal
-rwxr-xr-x 1 root root   888424 2025-04-20T21:06:12 pg_restore
-rwxr-xr-x 1 root root   613744 2025-04-20T21:06:12 pg_rewind
-rwxr-xr-x 1 root root   197160 2025-04-20T21:06:12 pg_test_fsync
-rwxr-xr-x 1 root root   154992 2025-04-20T21:06:12 pg_test_timing
-rwxr-xr-x 1 root root   640144 2025-04-20T21:06:13 pg_upgrade
-rwxr-xr-x 1 root root   408488 2025-04-20T21:06:13 pg_verifybackup
-rwxr-xr-x 1 root root   503720 2025-04-20T21:06:13 pg_waldump
-rwxr-xr-x 1 root root   271792 2025-04-20T21:06:13 pg_walsummary
-rwxr-xr-x 1 root root 48846360 2025-04-20T21:06:10 postgres
-rwxr-xr-x 1 root root  1962776 2025-04-20T21:06:13 psql
-rwxr-xr-x 1 root root   362120 2025-04-20T21:06:13 reindexdb
-rwxr-xr-x 1 root root   372904 2025-04-20T21:06:13 vacuumdb

[root@centos8-25 /glc/postgresql/app/postgresql-17.4/bin] # ls |xargs ldd -r
clusterdb:
	linux-vdso.so.1 (0x00007ffc7b1b1000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f80855f8000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f80853d8000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f8085013000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f8084d7f000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f8084896000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f8084514000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f80842bc000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f808584c000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f80840a5000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f8083ea1000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f8083c91000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f8083a7a000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f808385c000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f8083633000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f80833de000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f80830f4000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f8082edd000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f8082cd9000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f8082ac8000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f80828c4000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f808269a000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f8082416000)
createdb:
	linux-vdso.so.1 (0x00007ffc8b9a8000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f31a7cc0000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f31a7aa0000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f31a76db000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f31a7447000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f31a6f5e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f31a6bdc000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f31a6984000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f31a7f14000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f31a676d000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f31a6569000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f31a6359000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f31a6142000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f31a5f24000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f31a5cfb000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f31a5aa6000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f31a57bc000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f31a55a5000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f31a53a1000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f31a5190000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f31a4f8c000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f31a4d62000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f31a4ade000)
createuser:
	linux-vdso.so.1 (0x00007ffd94f20000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fb52f9c4000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb52f7a4000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb52f3df000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fb52f14b000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fb52ec62000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb52e8e0000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fb52e688000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb52fc18000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fb52e471000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fb52e26d000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fb52e05d000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fb52de46000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fb52dc28000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fb52d9ff000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fb52d7aa000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fb52d4c0000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fb52d2a9000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fb52d0a5000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fb52ce94000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fb52cc90000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fb52ca66000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fb52c7e2000)
dropdb:
	linux-vdso.so.1 (0x00007ffd04d82000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f65ffe85000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f65ffc65000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f65ff8a0000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f65ff60c000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f65ff123000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f65feda1000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f65feb49000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f66000d9000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f65fe932000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f65fe72e000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f65fe51e000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f65fe307000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f65fe0e9000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f65fdec0000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f65fdc6b000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f65fd981000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f65fd76a000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f65fd566000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f65fd355000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f65fd151000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f65fcf27000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f65fcca3000)
dropuser:
	linux-vdso.so.1 (0x00007ffe423eb000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f36759a0000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f3675780000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f36753bb000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f3675127000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f3674c3e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f36748bc000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f3674664000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f3675bf4000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f367444d000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f3674249000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f3674039000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f3673e22000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f3673c04000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f36739db000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f3673786000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f367349c000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f3673285000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f3673081000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f3672e70000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f3672c6c000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f3672a42000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f36727be000)
ecpg:
	linux-vdso.so.1 (0x00007ffe60b89000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fa96cc23000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa96c85e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa96ce43000)
initdb:
	linux-vdso.so.1 (0x00007ffc56574000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fb16bc6b000)
	libicui18n.so.60 => /lib64/libicui18n.so.60 (0x00007fb16b7aa000)
	libicuuc.so.60 => /lib64/libicuuc.so.60 (0x00007fb16b3e8000)
	libicudata.so.60 => /lib64/libicudata.so.60 (0x00007fb16983e000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb16961e000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fb169416000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb169094000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb168ccf000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fb168a3b000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fb168552000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fb1682fa000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fb1680f6000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fb167ce0000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fb167ac7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb16bebf000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fb1678b0000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fb1676a0000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fb167489000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fb16726b000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fb167042000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fb166ded000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fb166b03000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fb1668ec000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fb1666e8000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fb1664d7000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fb1662d3000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fb1660a9000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fb165e25000)
pg_amcheck:
	linux-vdso.so.1 (0x00007fffd6a63000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f73e484b000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f73e462b000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f73e4266000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f73e3fd2000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f73e3ae9000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f73e3767000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f73e350f000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f73e4a9f000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f73e32f8000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f73e30f4000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f73e2ee4000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f73e2ccd000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f73e2aaf000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f73e2886000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f73e2631000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f73e2347000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f73e2130000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f73e1f2c000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f73e1d1b000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f73e1b17000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f73e18ed000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f73e1669000)
pg_archivecleanup:
	linux-vdso.so.1 (0x00007fff7a383000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fbd5050d000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fbd50148000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fbd5072d000)
pg_basebackup:
	linux-vdso.so.1 (0x00007ffe48e95000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f9b3c77a000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f9b3c4d6000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f9b3c2b9000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f9b3c0a2000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f9b3be82000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f9b3babd000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f9b3b829000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f9b3b340000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f9b3afbe000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f9b3ad66000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f9b3c9ce000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f9b3ab62000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f9b3a952000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f9b3a73b000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f9b3a51d000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f9b3a2f4000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f9b3a09f000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f9b39db5000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f9b39b9e000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f9b3999a000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f9b39789000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f9b39585000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f9b3935b000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f9b390d7000)
pgbench:
	linux-vdso.so.1 (0x00007ffd60ba8000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f2a78755000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f2a7826c000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f2a7804c000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f2a77cca000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f2a77905000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f2a77671000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f2a77419000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f2a77202000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f2a76ffe000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f2a789a9000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f2a76dee000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f2a76bd7000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f2a769b9000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f2a76790000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f2a7653b000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f2a76251000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f2a7603a000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f2a75e36000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f2a75c25000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f2a75a21000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f2a757f7000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f2a75573000)
pg_checksums:
	linux-vdso.so.1 (0x00007ffd0a79e000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fa327086000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fa326e66000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa326aa1000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fa32680d000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fa326324000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fa325fa2000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fa325d4a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa3272da000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fa325b33000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fa32592f000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fa32571f000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fa325508000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fa3252ea000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fa3250c1000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fa324e6c000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fa324b82000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fa32496b000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fa324767000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fa324556000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fa324352000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fa324128000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fa323ea4000)
pg_combinebackup:
	linux-vdso.so.1 (0x00007fffeb5e8000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f0e06637000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f0e06417000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f0e06052000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f0e05e3b000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f0e05c37000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f0e06b20000)
pg_config:
	linux-vdso.so.1 (0x00007ffc5129e000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fc405d31000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc40596c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc405f51000)
pg_controldata:
	linux-vdso.so.1 (0x00007ffdd176e000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f9fca507000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f9fca142000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f9fca727000)
pg_createsubscriber:
	linux-vdso.so.1 (0x00007ffff3b81000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f62efde1000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f62efbc1000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f62ef83f000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f62ef47a000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f62ef1e6000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f62eecfd000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f62eeaa5000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f62f0035000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f62ee88e000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f62ee68a000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f62ee47a000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f62ee263000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f62ee045000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f62ede1c000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f62edbc7000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f62ed8dd000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f62ed6c6000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f62ed4c2000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f62ed2b1000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f62ed0ad000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f62ece83000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f62ecbff000)
pg_ctl:
	linux-vdso.so.1 (0x00007ffd76bdf000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fce890a6000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fce88ce1000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fce892c6000)
pg_dump:
	linux-vdso.so.1 (0x00007ffda67fe000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f7429ad1000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f742982d000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f7429610000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f74293f9000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f74291d9000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f7428e14000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f7428b80000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f7428697000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f7428315000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f74280bd000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7429d25000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f7427eb9000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f7427ca9000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f7427a92000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f7427874000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f742764b000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f74273f6000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f742710c000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f7426ef5000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f7426cf1000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f7426ae0000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f74268dc000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f74266b2000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f742642e000)
pg_dumpall:
	linux-vdso.so.1 (0x00007ffedefbc000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007ffacc25d000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007ffacc03d000)
	libc.so.6 => /lib64/libc.so.6 (0x00007ffacbc78000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007ffacb9e4000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007ffacb4fb000)
	libm.so.6 => /lib64/libm.so.6 (0x00007ffacb179000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007ffacaf21000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ffacc4b1000)
	libz.so.1 => /lib64/libz.so.1 (0x00007ffacad0a000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007ffacab06000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007ffaca8f6000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007ffaca6df000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007ffaca4c1000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007ffaca298000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007ffaca043000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007ffac9d59000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007ffac9b42000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007ffac993e000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007ffac972d000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007ffac9529000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007ffac92ff000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007ffac907b000)
pg_isready:
	linux-vdso.so.1 (0x00007ffd52546000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fb5d5463000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb5d5243000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb5d4e7e000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fb5d4bea000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fb5d4701000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb5d437f000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fb5d4127000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb5d56b7000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fb5d3f10000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fb5d3d0c000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fb5d3afc000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fb5d38e5000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fb5d36c7000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fb5d349e000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fb5d3249000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fb5d2f5f000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fb5d2d48000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fb5d2b44000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fb5d2933000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fb5d272f000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fb5d2505000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fb5d2281000)
pg_receivewal:
	linux-vdso.so.1 (0x00007fff0ddee000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fc012ad6000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007fc012832000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007fc012615000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fc0123fe000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fc0121de000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc011e19000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fc011b85000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fc01169c000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fc01131a000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fc0110c2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc012d2a000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fc010ebe000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fc010cae000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fc010a97000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fc010879000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fc010650000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fc0103fb000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fc010111000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fc00fefa000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fc00fcf6000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fc00fae5000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fc00f8e1000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fc00f6b7000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fc00f433000)
pg_recvlogical:
	linux-vdso.so.1 (0x00007ffc217f3000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f7ab6142000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f7ab5f25000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f7ab5d0e000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f7ab5aee000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f7ab5729000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f7ab5495000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f7ab4fac000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f7ab4c2a000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f7ab49d2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7ab6396000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f7ab47ce000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f7ab45be000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f7ab43a7000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f7ab4189000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f7ab3f60000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f7ab3d0b000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f7ab3a21000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f7ab380a000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f7ab3606000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f7ab33f5000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f7ab31f1000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f7ab2fc7000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f7ab2d43000)
pg_resetwal:
	linux-vdso.so.1 (0x00007ffe42f79000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f2d563ab000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f2d55fe6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f2d565cb000)
pg_restore:
	linux-vdso.so.1 (0x00007ffce0989000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f632c468000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f632c1c4000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f632bfa7000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f632bd90000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f632bb70000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f632b7ab000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f632b517000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f632b02e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f632acac000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f632aa54000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f632c6bc000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f632a850000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f632a640000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f632a429000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f632a20b000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f6329fe2000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f6329d8d000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f6329aa3000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f632988c000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f6329688000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f6329477000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f6329273000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f6329049000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f6328dc5000)
pg_rewind:
	linux-vdso.so.1 (0x00007ffedcf8a000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f78f2a76000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f78f27d2000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f78f25b5000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f78f2395000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f78f1fd0000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f78f1d3c000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f78f1853000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f78f14d1000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f78f1279000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f78f2cca000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f78f1062000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f78f0e5e000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f78f0c4e000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f78f0a37000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f78f0819000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f78f05f0000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f78f039b000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f78f00b1000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f78efe9a000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f78efc96000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f78efa85000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f78ef881000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f78ef657000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f78ef3d3000)
pg_test_fsync:
	linux-vdso.so.1 (0x00007fff735eb000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f0e26b2f000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f0e267ad000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f0e263e8000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f0e26d4f000)
pg_test_timing:
	linux-vdso.so.1 (0x00007f98b71c4000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f98b6d7a000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f98b69b5000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f98b6f9a000)
pg_upgrade:
	linux-vdso.so.1 (0x00007ffd11147000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fa354465000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fa354245000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa353e80000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fa353bec000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fa353703000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fa353381000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fa353129000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa3546b9000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fa352f12000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fa352d0e000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fa352afe000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fa3528e7000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fa3526c9000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fa3524a0000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fa35224b000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fa351f61000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fa351d4a000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fa351b46000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fa351935000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fa351731000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fa351507000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007fa351283000)
pg_verifybackup:
	linux-vdso.so.1 (0x00007ffc85f61000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f8e39744000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f8e3925b000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f8e3903b000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f8e38c76000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f8e389e2000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f8e38660000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f8e38408000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f8e381f1000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f8e37fed000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f8e39998000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f8e37ddd000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f8e37bc6000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f8e379a8000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f8e3777f000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f8e3752a000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f8e37240000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f8e37029000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f8e36e25000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f8e36c14000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f8e36a10000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f8e367e6000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f8e36562000)
pg_waldump:
	linux-vdso.so.1 (0x00007ffc8454f000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f1a5b929000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f1a5b70c000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f1a5b4ec000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f1a5b127000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f1a5bbcd000)
pg_walsummary:
	linux-vdso.so.1 (0x00007ffc3708e000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f2c97c9b000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f2c978d6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f2c97ebb000)
postgres:
	linux-vdso.so.1 (0x00007ffc64966000)
	libzstd.so.1 => /lib64/libzstd.so.1 (0x00007f7622780000)
	liblz4.so.1 => /lib64/liblz4.so.1 (0x00007f7622563000)
	libxml2.so.2 => /lib64/libxml2.so.2 (0x00007f76221fb000)
	libpam.so.0 => /lib64/libpam.so.0 (0x00007f7621feb000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f7621d57000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f762186e000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f7621657000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f7621437000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f762122f000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f762102b000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f7620ca9000)
	libldap-2.4.so.2 => /lib64/libldap-2.4.so.2 (0x00007f7620a5a000)
	libicui18n.so.60 => /lib64/libicui18n.so.60 (0x00007f7620599000)
	libicuuc.so.60 => /lib64/libicuuc.so.60 (0x00007f76201d7000)
	libsystemd.so.0 => /lib64/libsystemd.so.0 (0x00007f761fe8e000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f761fac9000)
	liblzma.so.5 => /lib64/liblzma.so.5 (0x00007f761f8a2000)
	libaudit.so.1 => /lib64/libaudit.so.1 (0x00007f761f678000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7622a24000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f761f468000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f761f251000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f761f033000)
	libicudata.so.60 => /lib64/libicudata.so.60 (0x00007f761d489000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f761d073000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f761ce5a000)
	libcap.so.2 => /lib64/libcap.so.2 (0x00007f761cc54000)
	libmount.so.1 => /lib64/libmount.so.1 (0x00007f761c9fa000)
	libgcrypt.so.20 => /lib64/libgcrypt.so.20 (0x00007f761c6dc000)
	libcap-ng.so.0 => /lib64/libcap-ng.so.0 (0x00007f761c4d6000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f761c2ad000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f761c058000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f761bd6e000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f761bb57000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f761b953000)
	libblkid.so.1 => /lib64/libblkid.so.1 (0x00007f761b700000)
	libuuid.so.1 => /lib64/libuuid.so.1 (0x00007f761b4f8000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f761b2ce000)
	libgpg-error.so.0 => /lib64/libgpg-error.so.0 (0x00007f761b0ad000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f761ae9c000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f761ac98000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f761aa14000)
psql:
	linux-vdso.so.1 (0x00007fffec5ba000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f71c3077000)
	libreadline.so.7 => /lib64/libreadline.so.7 (0x00007f71c2e28000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f71c2c08000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f71c2886000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f71c24c1000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f71c222d000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f71c1d44000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f71c1aec000)
	libtinfo.so.6 => /lib64/libtinfo.so.6 (0x00007f71c18bf000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f71c32cb000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f71c16a8000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f71c14a4000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f71c1294000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f71c107d000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f71c0e5f000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f71c0c36000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f71c09e1000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f71c06f7000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f71c04e0000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f71c02dc000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f71c00cb000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f71bfec7000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f71bfc9d000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f71bfa19000)
reindexdb:
	linux-vdso.so.1 (0x00007fff590db000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007f3faa713000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f3faa4f3000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f3faa12e000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f3fa9e9a000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f3fa99b1000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f3fa962f000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007f3fa93d7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f3faa967000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f3fa91c0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f3fa8fbc000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007f3fa8dac000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f3fa8b95000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007f3fa8977000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f3fa874e000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007f3fa84f9000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007f3fa820f000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007f3fa7ff8000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007f3fa7df4000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007f3fa7be3000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007f3fa79df000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f3fa77b5000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x00007f3fa7531000)
vacuumdb:
	linux-vdso.so.1 (0x00007ffc3dbb0000)
	libpq.so.5 => /glc/postgresql/app/postgresql-17.4/lib/libpq.so.5 (0x00007fec5e732000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fec5e512000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fec5e14d000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fec5deb9000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fec5d9d0000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fec5d64e000)
	libldap_r-2.4.so.2 => /lib64/libldap_r-2.4.so.2 (0x00007fec5d3f6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fec5e986000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fec5d1df000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fec5cfdb000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x00007fec5cdcb000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fec5cbb4000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x00007fec5c996000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fec5c76d000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fec5c518000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fec5c22e000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fec5c017000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fec5be13000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fec5bc02000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fec5b9fe000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fec5b7d4000)

```




```bash
[root@centos8-25 /glc/postgresql/app/postgresql-17.4/bin] # readelf -d postgres

Dynamic section at offset 0x8eed90 contains 33 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libz.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libicui18n.so.60]
 0x0000000000000001 (NEEDED)             Shared library: [libicuuc.so.60]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000001d (RUNPATH)            Library runpath: [/glc/postgresql/app/postgresql-17.4/lib]
 0x000000000000000c (INIT)               0x4a1b60
 0x000000000000000d (FINI)               0x9b4ae4
 0x0000000000000019 (INIT_ARRAY)         0xeeed70
 0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)
 0x000000000000001a (FINI_ARRAY)         0xeeed78
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x400278
 0x000000006ffffef5 (GNU_HASH)           0x412f68
 0x0000000000000005 (STRTAB)             0x468390
 0x0000000000000006 (SYMTAB)             0x4277d0
 0x000000000000000a (STRSZ)              205821 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0xeef000
 0x0000000000000002 (PLTRELSZ)           7008 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x4a0000
 0x0000000000000007 (RELA)               0x49ff10
 0x0000000000000008 (RELASZ)             240 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x49fde0
 0x000000006fffffff (VERNEEDNUM)         5
 0x000000006ffffff0 (VERSYM)             0x49a78e
 0x0000000000000000 (NULL)               0x0

```



###### pg_config

```bash
[root@centos8-25 /glc/postgresql/app/postgresql-17.4/bin] # ./pg_config
BINDIR = /glc/postgresql/app/postgresql-17.4/bin
DOCDIR = /glc/postgresql/app/postgresql-17.4/share/doc
HTMLDIR = /glc/postgresql/app/postgresql-17.4/share/doc
INCLUDEDIR = /glc/postgresql/app/postgresql-17.4/include
PKGINCLUDEDIR = /glc/postgresql/app/postgresql-17.4/include
INCLUDEDIR-SERVER = /glc/postgresql/app/postgresql-17.4/include/server
LIBDIR = /glc/postgresql/app/postgresql-17.4/lib
PKGLIBDIR = /glc/postgresql/app/postgresql-17.4/lib
LOCALEDIR = /glc/postgresql/app/postgresql-17.4/share/locale
MANDIR = /glc/postgresql/app/postgresql-17.4/share/man
SHAREDIR = /glc/postgresql/app/postgresql-17.4/share
SYSCONFDIR = /glc/postgresql/app/postgresql-17.4/etc
PGXS = /glc/postgresql/app/postgresql-17.4/lib/pgxs/src/makefiles/pgxs.mk
CONFIGURE =  '--prefix=/glc/postgresql/app/postgresql-17.4' 'PKG_CONFIG_PATH=/glc/mysql/app/mysql-8.0.32/lib/pkgconfig:/glc/rpcgen/app/rpcgen-1.4.4/lib/pkgconfig:/glc/gcc/app/gcc-11.4.0/lib/pkgconfig:/glc/isl/app/isl-0.18/lib/pkgconfig:/glc/mpc/app/mpc-1.0.3/lib/pkgconfig:/glc/mpfr/app/mpfr-3.1.6/lib/pkgconfig:/glc/gmp/app/gmp-6.1.0/lib/pkgconfig:/8b9ffbd8dc10295ca7fcbff89ec73346'
CC = gcc
CPPFLAGS = -D_GNU_SOURCE
CFLAGS = -Wall -Wmissing-prototypes -Wpointer-arith -Wdeclaration-after-statement -Werror=vla -Wendif-labels -Wmissing-format-attribute -Wimplicit-fallthrough=3 -Wcast-function-type -Wshadow=compatible-local -Wformat-security -fno-strict-aliasing -fwrapv -fexcess-precision=standard -Wno-format-truncation -Wno-stringop-truncation -O2
CFLAGS_SL = -fPIC
LDFLAGS = -Wl,--as-needed -Wl,-rpath,'/glc/postgresql/app/postgresql-17.4/lib',--enable-new-dtags
LDFLAGS_EX = 
LDFLAGS_SL = 
LIBS = -lpgcommon -lpgport -lz -lreadline -lpthread -lrt -ldl -lm 
VERSION = PostgreSQL 17.4

```
---

以下是 PostgreSQL 17.4 版本中 `bin` 目录下所有二进制文件的详细功能说明：

---

### **核心工具**
1. **postgres**  
   PostgreSQL 服务器主程序，通常由 `pg_ctl` 启动，不直接手动运行。

2. **initdb**  
   初始化一个新的 PostgreSQL 数据库集群（即创建数据目录、生成系统表等）。

3. **pg_ctl**  
   数据库服务控制工具，用于启动、停止、重启 PostgreSQL 服务或查看服务状态。  
   示例：`pg_ctl start -D /path/to/data_directory`

4. **psql**  
   PostgreSQL 交互式命令行客户端，用于执行 SQL 命令和管理数据库。

---

### **备份与恢复**
5. **pg_dump**  
   将单个数据库导出为 SQL 脚本或归档文件格式（支持自定义/目录/压缩格式）。  
   示例：`pg_dump -U username -d dbname > backup.sql`

6. **pg_dumpall**  
   导出所有数据库（包括全局对象如角色和表空间）到 SQL 脚本。

7. **pg_restore**  
   从 `pg_dump` 生成的归档文件中恢复数据库，支持选择性恢复。

8. **pg_basebackup**  
   创建集群的物理备份（二进制格式），用于搭建流复制或 PITR（时间点恢复）。

9. **pg_verifybackup**  
   验证 `pg_basebackup` 生成的备份完整性。

10. **pg_combinebackup**  
    合并多个增量备份（PostgreSQL 17 引入的新功能，用于增量备份管理）。

---

### **WAL（预写式日志）管理**
11. **pg_receivewal**  
    通过流式协议从主服务器接收 WAL 日志，用于日志归档或备用服务器。

12. **pg_recvlogical**  
    接收逻辑解码流（用于逻辑复制或 CDC）。

13. **pg_waldump**  
    解析并显示 WAL 日志内容，用于调试或分析。

14. **pg_archivecleanup**  
    清理过时的 WAL 归档文件（通常与 PITR 配合使用）。

15. **pg_walsummary**  
    生成 WAL 摘要信息（PostgreSQL 17 新增，用于优化备份）。

---

### **集群维护**
16. **pg_resetwal**  
    重置 WAL 日志（修复因 WAL 损坏导致数据库无法启动的问题，慎用！）。

17. **pg_rewind**  
    将主数据库与备用数据库同步（当备用库落后时使用）。

18. **pg_controldata**  
    显示数据目录的控制文件信息（如检查点、WAL 状态等）。

---

### **性能与调试**
19. **pgbench**  
    内置基准测试工具，用于模拟数据库负载。

20. **pg_test_fsync**  
    测试系统上不同同步方法的性能（影响数据持久性配置）。

21. **pg_test_timing**  
    测试系统时钟的稳定性和准确性（对性能分析关键）。

22. **pg_amcheck**  
    检查表或索引的逻辑一致性（数据损坏检测）。

23. **pg_checksums**  
    启用/禁用数据页校验和（检测物理损坏）。

---

### **用户与角色管理**
24. **createuser**  
    创建新数据库角色（等同于 `CREATE ROLE` SQL 命令）。

25. **dropuser**  
    删除数据库角色（等同于 `DROP ROLE` SQL 命令）。

---

### **数据库管理**
26. **createdb**  
    创建新数据库（等同于 `CREATE DATABASE` SQL 命令）。

27. **dropdb**  
    删除数据库（等同于 `DROP DATABASE` SQL 命令）。

28. **clusterdb**  
    对表重新聚类（按索引顺序重排数据，等同于 `CLUSTER` SQL 命令）。

29. **vacuumdb**  
    执行 VACUUM 或 ANALYZE（清理死元组或更新统计信息）。

30. **reindexdb**  
    重建索引（等同于 `REINDEX` SQL 命令）。

---

### **开发与扩展**
31. **ecpg**  
    Embedded SQL in C 预处理器，用于将 SQL 语句嵌入 C 程序。

32. **pg_config**  
    显示 PostgreSQL 编译时的配置参数（如安装路径、编译选项等）。

---

### **高可用性（PostgreSQL 17 新增）**
33. **pg_createsubscriber**  
    快速创建物理复制订阅者（简化流复制配置）。

---

### **其他工具**
34. **pg_isready**  
    检查数据库服务器是否接受连接（返回状态码）。

35. **pg_upgrade**  
    跨版本升级数据库集群（需停机，谨慎操作）。

---

### **使用建议**
- **日常管理**：优先使用 `psql` 或封装工具（如 `pgAdmin`）。
- **备份恢复**：逻辑备份用 `pg_dump`/`pg_restore`，物理备份用 `pg_basebackup`。
- **故障修复**：`pg_resetwal` 和 `pg_rewind` 是最后手段，需充分理解风险。

如需更详细的参数说明，可通过 `--help` 选项查看帮助（如 `pg_dump --help`）。





## 源码安装

- https://zhuanlan.zhihu.com/p/407691454


#### configure

```bash
[root@x86centos7-250 /glc/postgresql/source/postgresql-17.4] # ./configure --help
`configure' configures PostgreSQL 17.4 to adapt to many kinds of systems.

Usage: ./configure [OPTION]... [VAR=VALUE]...

To assign environment variables (e.g., CC, CFLAGS...), specify them as
VAR=VALUE.  See below for descriptions of some of the useful variables.

Defaults for the options are specified in brackets.

Configuration:
  -h, --help              display this help and exit
      --help=short        display options specific to this package
      --help=recursive    display the short help of all the included packages
  -V, --version           display version information and exit
  -q, --quiet, --silent   do not print `checking ...' messages
      --cache-file=FILE   cache test results in FILE [disabled]
  -C, --config-cache      alias for `--cache-file=config.cache'
  -n, --no-create         do not create output files
      --srcdir=DIR        find the sources in DIR [configure dir or `..']

Installation directories:
  --prefix=PREFIX         install architecture-independent files in PREFIX
                          [/usr/local/pgsql]
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                          [PREFIX]

By default, `make install' will install all the files in
`/usr/local/pgsql/bin', `/usr/local/pgsql/lib' etc.  You can specify
an installation prefix other than `/usr/local/pgsql' using `--prefix',
for instance `--prefix=$HOME'.

For better control, use the options below.

Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libexecdir=DIR        program executables [EPREFIX/libexec]
  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info documentation [DATAROOTDIR/info]
  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
  --mandir=DIR            man documentation [DATAROOTDIR/man]
  --docdir=DIR            documentation root [DATAROOTDIR/doc/postgresql]
  --htmldir=DIR           html documentation [DOCDIR]
  --dvidir=DIR            dvi documentation [DOCDIR]
  --pdfdir=DIR            pdf documentation [DOCDIR]
  --psdir=DIR             ps documentation [DOCDIR]

System types:
  --build=BUILD     configure for building on BUILD [guessed]
  --host=HOST       cross-compile to build programs to run on HOST [BUILD]

Optional Features:
  --disable-option-checking  ignore unrecognized --enable/--with options
  --disable-FEATURE       do not include FEATURE (same as --enable-FEATURE=no)
  --enable-FEATURE[=ARG]  include FEATURE [ARG=yes]
  --disable-integer-datetimes
                          obsolete option, no longer supported
  --enable-nls[=LANGUAGES]
                          enable Native Language Support
  --disable-rpath         do not embed shared library search path in
                          executables
  --disable-spinlocks     do not use spinlocks
  --disable-atomics       do not use atomic operations
  --enable-debug          build with debugging symbols (-g)
  --enable-profiling      build with profiling enabled
  --enable-coverage       build with coverage testing instrumentation
  --enable-dtrace         build with DTrace support
  --enable-tap-tests      enable TAP tests (requires Perl and IPC::Run)
  --enable-injection-points
                          enable injection points (for testing)
  --enable-depend         turn on automatic dependency tracking
  --enable-cassert        enable assertion checks (for debugging)
  --disable-largefile     omit support for large files

Optional Packages:
  --with-PACKAGE[=ARG]    use PACKAGE [ARG=yes]
  --without-PACKAGE       do not use PACKAGE (same as --with-PACKAGE=no)
  --with-extra-version=STRING
                          append STRING to version
  --with-template=NAME    override operating system template
  --with-includes=DIRS    look for additional header files in DIRS
  --with-libraries=DIRS   look for additional libraries in DIRS
  --with-libs=DIRS        alternative spelling of --with-libraries
  --with-pgport=PORTNUM   set default port number [5432]
  --with-blocksize=BLOCKSIZE
                          set table block size in kB [8]
  --with-segsize=SEGSIZE  set table segment size in GB [1]
  --with-segsize-blocks=SEGSIZE_BLOCKS
                          set table segment size in blocks [0]
  --with-wal-blocksize=BLOCKSIZE
                          set WAL block size in kB [8]
  --with-llvm             build with LLVM based JIT support
  --without-icu           build without ICU support
  --with-tcl              build Tcl modules (PL/Tcl)
  --with-tclconfig=DIR    tclConfig.sh is in DIR
  --with-perl             build Perl modules (PL/Perl)
  --with-python           build Python modules (PL/Python)
  --with-gssapi           build with GSSAPI support
  --with-krb-srvnam=NAME  default service principal name in Kerberos (GSSAPI)
                          [postgres]
  --with-pam              build with PAM support
  --with-bsd-auth         build with BSD Authentication support
  --with-ldap             build with LDAP support
  --with-bonjour          build with Bonjour support
  --with-selinux          build with SELinux support
  --with-systemd          build with systemd support
  --without-readline      do not use GNU Readline nor BSD Libedit for editing
  --with-libedit-preferred
                          prefer BSD Libedit over GNU Readline
  --with-uuid=LIB         build contrib/uuid-ossp using LIB (bsd,e2fs,ossp)
  --with-ossp-uuid        obsolete spelling of --with-uuid=ossp
  --with-libxml           build with XML support
  --with-libxslt          use XSLT support when building contrib/xml2
  --with-system-tzdata=DIR
                          use system time zone data in DIR
  --without-zlib          do not use Zlib
  --with-lz4              build with LZ4 support
  --with-zstd             build with ZSTD support
  --with-ssl=LIB          use LIB for SSL/TLS support (openssl)
  --with-openssl          obsolete spelling of --with-ssl=openssl

Some influential environment variables:
  CC          C compiler command
  CFLAGS      C compiler flags
  LDFLAGS     linker flags, e.g. -L<lib dir> if you have libraries in a
              nonstandard directory <lib dir>
  LIBS        libraries to pass to the linker, e.g. -l<library>
  CPPFLAGS    (Objective) C/C++ preprocessor flags, e.g. -I<include dir> if
              you have headers in a nonstandard directory <include dir>
  CXX         C++ compiler command
  CXXFLAGS    C++ compiler flags
  LLVM_CONFIG path to llvm-config command
  CLANG       path to clang compiler to generate bitcode
  CPP         C preprocessor
  PKG_CONFIG  path to pkg-config utility
  PKG_CONFIG_PATH
              directories to add to pkg-config's search path
  PKG_CONFIG_LIBDIR
              path overriding pkg-config's built-in search path
  ICU_CFLAGS  C compiler flags for ICU, overriding pkg-config
  ICU_LIBS    linker flags for ICU, overriding pkg-config
  XML2_CONFIG path to xml2-config utility
  XML2_CFLAGS C compiler flags for XML2, overriding pkg-config
  XML2_LIBS   linker flags for XML2, overriding pkg-config
  LZ4_CFLAGS  C compiler flags for LZ4, overriding pkg-config
  LZ4_LIBS    linker flags for LZ4, overriding pkg-config
  ZSTD_CFLAGS C compiler flags for ZSTD, overriding pkg-config
  ZSTD_LIBS   linker flags for ZSTD, overriding pkg-config
  LDFLAGS_EX  extra linker flags for linking executables only
  LDFLAGS_SL  extra linker flags for linking shared libraries only
  PERL        Perl program
  PYTHON      Python program
  MSGFMT      msgfmt program for NLS
  TCLSH       Tcl interpreter program (tclsh)

Use these variables to override the choices made by `configure' or to help
it to find libraries and programs with nonstandard names/locations.

Report bugs to <pgsql-bugs@lists.postgresql.org>.
PostgreSQL home page: <https://www.postgresql.org/>.

```













