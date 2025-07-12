---
title: openssl
description: openssl
date: 2023-11-06
weight: 30000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
[openssl github](https://github.com/openssl/openssl)
[openssl 官网](http://www.openssl.org)
{{%/pageinfo%}}



{{<note>}}
<!---->
**依赖如下**：
- make
- Perl 5
- gcc

{{</note>}}




## 1. 依赖

依赖会随着环境不同而不同，最主要是依赖glibc，然后是依赖zlib，当然还需要编译工具



```viz-dot
digraph "openssl" {
node [
        fontsize = "12"
];
    "openssl" [ label = "openssl", shape = octagon ];
    "libssl.so.1.1" [ label = "libssl.so.1.1", shape = octagon ];
    "libcrypto.so.1.1" [ label = "libcrypto.so.1.1", shape = octagon ];
    "libpthread.so.0" [ label = "libpthread.so.0", shape = octagon ];


    "openssl" -> "libssl.so.1.1";
    "openssl" -> "libcrypto.so.1.1";
    "openssl" -> "libpthread.so.0";
    "libssl.so.1.1" -> "libpthread.so.0";
    "libcrypto.so.1.1" -> "libpthread.so.0";


}
```

```bash
2025-03-27T22:29:14 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t/bin] # ldd openssl
	linux-vdso.so.1 (0x0000ffff023c0000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x0000ffff02300000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000ffff02030000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000ffff02000000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000ffff01fc0000)
	libc.so.6 => /lib64/libc.so.6 (0x0000ffff01e30000)
	libz.so.1 => /lib64/libz.so.1 (0x0000ffff01df0000)
	/lib/ld-linux-aarch64.so.1 (0x0000ffff023d0000)
2025-03-27T22:35:05 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t/bin] # ldd c_rehash
	not a dynamic executable
2025-03-27T22:35:12 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t/bin] #


2025-03-27T22:35:55 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t/lib] # find ./ -name "*.so*" |xargs ldd
./libcrypto.so.1.1:
	linux-vdso.so.1 (0x0000fffe38780000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffe38460000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffe38420000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffe38290000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffe38790000)
./libssl.so.1.1:
	linux-vdso.so.1 (0x0000fffe17000000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000fffe16c70000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffe16c40000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffe16c00000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffe16a70000)
	libz.so.1 => /lib64/libz.so.1 (0x0000fffe16a30000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffe17010000)
./libcrypto.so:
	linux-vdso.so.1 (0x0000fffd73a60000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffd73740000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffd73700000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffd73570000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffd73a70000)
./libssl.so:
	linux-vdso.so.1 (0x0000fffec2f50000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000fffec2bc0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffec2b90000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffec2b50000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffec29c0000)
	libz.so.1 => /lib64/libz.so.1 (0x0000fffec2980000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffec2f60000)
./engines-1.1/afalg.so:
	linux-vdso.so.1 (0x0000fffe5bf90000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000fffe5bc80000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffe5bc50000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffe5bc10000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffe5ba80000)
	libz.so.1 => /lib64/libz.so.1 (0x0000fffe5ba40000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffe5bfa0000)
./engines-1.1/capi.so:
	linux-vdso.so.1 (0x0000fffc222f0000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000fffc21fe0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffc21fb0000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffc21f70000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffc21de0000)
	libz.so.1 => /lib64/libz.so.1 (0x0000fffc21da0000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffc22300000)
./engines-1.1/padlock.so:
	linux-vdso.so.1 (0x0000ffff074e0000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x0000ffff071d0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000ffff071a0000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000ffff07160000)
	libc.so.6 => /lib64/libc.so.6 (0x0000ffff06fd0000)
	libz.so.1 => /lib64/libz.so.1 (0x0000ffff06f90000)
	/lib/ld-linux-aarch64.so.1 (0x0000ffff074f0000)
2025-03-27T22:36:20 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t/lib] #
```






## 2. 内容


### 2.1 bin lib

```viz-dot
digraph "openssl-package" {
node [
        fontsize = "12"
];
"openssl-package" [ label = "openssl-package", shape = octagon ];
"openssl" [ label = "openssl", shape = octagon ];
"c_rehash" [ label = "c_rehash", shape = octagon ];
"libssl.so.1.1" [ label = "libssl.so.1.1", shape = octagon ];
"libcrypto.so.1.1" [ label = "libcrypto.so.1.1", shape = octagon ];
"libcrypto.pc" [ label = "libcrypto.pc", shape = octagon ];
"openssl.pc" [ label = "openssl.pc", shape = octagon ];
"libssl.pc" [ label = "libssl.pc", shape = octagon ];

"openssl-package" -> "openssl";
"openssl-package" -> "c_rehash";
"openssl-package" -> "libssl.pc";
"openssl-package" -> "openssl.pc";
"openssl-package" -> "libcrypto.pc";
"openssl-package" -> "libssl.so.1.1";
"openssl-package" -> "libcrypto.so.1.1";

}
```

```bash
2025-03-27T22:27:27 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t] # tree bin lib
bin
├── c_rehash
└── openssl
lib
├── engines-1.1
│   ├── afalg.so
│   ├── capi.so
│   └── padlock.so
├── libcrypto.a
├── libcrypto.so -> libcrypto.so.1.1
├── libcrypto.so.1.1
├── libssl.a
├── libssl.so -> libssl.so.1.1
├── libssl.so.1.1
└── pkgconfig
    ├── libcrypto.pc
    ├── libssl.pc
    └── openssl.pc

```









### 2.2 include

- openssl/aes.h
- openssl/asn1err.h
- openssl/asn1.h
- openssl/asn1_mac.h
- openssl/asn1t.h
- openssl/asyncerr.h
- openssl/async.h
- openssl/bioerr.h
- openssl/bio.h
- openssl/blowfish.h
- openssl/bnerr.h
- openssl/bn.h
- openssl/buffererr.h
- openssl/buffer.h
- openssl/camellia.h
- openssl/cast.h
- openssl/cmac.h
- openssl/cmserr.h
- openssl/cms.h
- openssl/comperr.h
- openssl/comp.h
- openssl/conf_api.h
- openssl/conferr.h
- openssl/conf.h
- openssl/cryptoerr.h
- openssl/crypto.h
- openssl/cterr.h
- openssl/ct.h
- openssl/des.h
- openssl/dherr.h
- openssl/dh.h
- openssl/dsaerr.h
- openssl/dsa.h
- openssl/dtls1.h
- openssl/ebcdic.h
- openssl/ecdh.h
- openssl/ecdsa.h
- openssl/ecerr.h
- openssl/ec.h
- openssl/engineerr.h
- openssl/engine.h
- openssl/e_os2.h
- openssl/err.h
- openssl/evperr.h
- openssl/evp.h
- openssl/hmac.h
- openssl/idea.h
- openssl/kdferr.h
- openssl/kdf.h
- openssl/lhash.h
- openssl/md2.h
- openssl/md4.h
- openssl/md5.h
- openssl/mdc2.h
- openssl/modes.h
- openssl/objectserr.h
- openssl/objects.h
- openssl/obj_mac.h
- openssl/ocsperr.h
- openssl/ocsp.h
- openssl/opensslconf.h
- openssl/opensslv.h
- openssl/ossl_typ.h
- openssl/pem2.h
- openssl/pemerr.h
- openssl/pem.h
- openssl/pkcs12err.h
- openssl/pkcs12.h
- openssl/pkcs7err.h
- openssl/pkcs7.h
- openssl/rand_drbg.h
- openssl/randerr.h
- openssl/rand.h
- openssl/rc2.h
- openssl/rc4.h
- openssl/rc5.h
- openssl/ripemd.h
- openssl/rsaerr.h
- openssl/rsa.h
- openssl/safestack.h
- openssl/seed.h
- openssl/sha.h
- openssl/srp.h
- openssl/srtp.h
- openssl/ssl2.h
- openssl/ssl3.h
- openssl/sslerr.h
- openssl/ssl.h
- openssl/stack.h
- openssl/storeerr.h
- openssl/store.h
- openssl/symhacks.h
- openssl/tls1.h
- openssl/tserr.h
- openssl/ts.h
- openssl/txt_db.h
- openssl/uierr.h
- openssl/ui.h
- openssl/whrlpool.h
- openssl/x509err.h
- openssl/x509.h
- openssl/x509v3err.h
- openssl/x509v3.h
- openssl/x509_vfy.h



## 3. 安装


#### 3.1 yum安装

```bash
yum -y install openssl openssl-devel

```

#### 3.2 openssl

```bash
2025-03-23T16:01:46 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w] # ll
total 20
drwxr-xr-x 2 mysql mysql 4096 2025-03-22T11:33:50 bin
drwxr-xr-x 3 mysql mysql 4096 2025-03-22T11:33:49 include
drwxr-xr-x 4 mysql mysql 4096 2025-03-22T11:33:50 lib
drwxr-xr-x 4 mysql mysql 4096 2025-03-22T11:34:17 share
drwxr-xr-x 5 mysql mysql 4096 2025-03-22T11:33:50 ssl
2025-03-23T16:01:47 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w] # cd lib/
2025-03-23T16:01:50 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/lib] # ll
total 10532
drwxr-xr-x 2 mysql mysql    4096 2025-03-22T11:33:50 engines-1.1
-rw-r--r-- 1 mysql mysql 5652502 2025-03-22T11:33:49 libcrypto.a
lrwxrwxrwx 1 mysql mysql      16 2025-03-22T11:33:49 libcrypto.so -> libcrypto.so.1.1
-rwxr-xr-x 1 mysql mysql 3393072 2025-03-22T11:33:49 libcrypto.so.1.1
-rw-r--r-- 1 mysql mysql 1028514 2025-03-22T11:33:49 libssl.a
lrwxrwxrwx 1 mysql mysql      13 2025-03-22T11:33:49 libssl.so -> libssl.so.1.1
-rwxr-xr-x 1 mysql mysql  689576 2025-03-22T11:33:49 libssl.so.1.1
drwxr-xr-x 2 mysql mysql    4096 2025-03-22T11:33:49 pkgconfig

2025-03-23T16:01:51 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/lib] # ldd libssl.so.1.1
	linux-vdso.so.1 =>  (0x00007ffc40560000)
	libcrypto.so.1.1 => /glc/openssl/app/openssl-1.1.1w/lib/libcrypto.so.1.1 (0x00007fcdde0a1000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fcddde9d000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fcdddc81000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fcddd8b3000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fcdde820000)

2025-03-23T16:04:13 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/lib] # ldd libcrypto.so.1.1
	linux-vdso.so.1 =>  (0x00007ffe7c1f8000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f19c0344000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f19c0128000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f19bfd5a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f19c0a34000)

2025-03-23T16:04:20 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/lib] # cd ..
2025-03-23T16:04:23 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w] # ll
total 20
drwxr-xr-x 2 mysql mysql 4096 2025-03-22T11:33:50 bin
drwxr-xr-x 3 mysql mysql 4096 2025-03-22T11:33:49 include
drwxr-xr-x 4 mysql mysql 4096 2025-03-22T11:33:50 lib
drwxr-xr-x 4 mysql mysql 4096 2025-03-22T11:34:17 share
drwxr-xr-x 5 mysql mysql 4096 2025-03-22T11:33:50 ssl
2025-03-23T16:04:24 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w] # cd bin/
2025-03-23T16:04:28 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/bin] # ll
total 740
-rwxr-xr-x 1 mysql mysql   6722 2025-03-22T11:33:50 c_rehash
-rwxr-xr-x 1 mysql mysql 749088 2025-03-22T11:33:50 openssl
2025-03-23T16:04:29 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/bin] # ldd openssl
	linux-vdso.so.1 =>  (0x00007ffd21fce000)
	libssl.so.1.1 => /glc/openssl/app/openssl-1.1.1w/lib/libssl.so.1.1 (0x00007fa6a9e45000)
	libcrypto.so.1.1 => /glc/openssl/app/openssl-1.1.1w/lib/libcrypto.so.1.1 (0x00007fa6a9959000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fa6a9755000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fa6a9539000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa6a916b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa6aa0d8000)
2025-03-23T16:04:40 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/bin] # ldd c_rehash
	not a dynamic executable
2025-03-23T16:04:46 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/bin] # cd ..
2025-03-23T16:04:51 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w] # cd include/
2025-03-23T16:04:55 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/include] # ll
total 4
drwxr-xr-x 2 mysql mysql 4096 2025-03-22T11:33:49 openssl
2025-03-23T16:04:56 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/include] # cd openssl/
2025-03-23T16:04:58 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/include/openssl] # ll
total 1560
-rw-r--r-- 1 mysql mysql   3349 2025-03-22T11:33:49 aes.h
-rw-r--r-- 1 mysql mysql  14687 2025-03-22T11:33:49 asn1err.h
-rw-r--r-- 1 mysql mysql  33627 2025-03-22T11:33:49 asn1.h
-rw-r--r-- 1 mysql mysql    395 2025-03-22T11:33:49 asn1_mac.h
-rw-r--r-- 1 mysql mysql  32940 2025-03-22T11:33:49 asn1t.h
-rw-r--r-- 1 mysql mysql   1326 2025-03-22T11:33:49 asyncerr.h
-rw-r--r-- 1 mysql mysql   2398 2025-03-22T11:33:49 async.h
-rw-r--r-- 1 mysql mysql   6400 2025-03-22T11:33:49 bioerr.h
-rw-r--r-- 1 mysql mysql  34907 2025-03-22T11:33:49 bio.h
-rw-r--r-- 1 mysql mysql   1847 2025-03-22T11:33:49 blowfish.h
-rw-r--r-- 1 mysql mysql   4969 2025-03-22T11:33:49 bnerr.h
-rw-r--r-- 1 mysql mysql  22135 2025-03-22T11:33:49 bn.h
-rw-r--r-- 1 mysql mysql    820 2025-03-22T11:33:49 buffererr.h
-rw-r--r-- 1 mysql mysql   1600 2025-03-22T11:33:49 buffer.h
-rw-r--r-- 1 mysql mysql   3179 2025-03-22T11:33:49 camellia.h
-rw-r--r-- 1 mysql mysql   1674 2025-03-22T11:33:49 cast.h
-rw-r--r-- 1 mysql mysql   1064 2025-03-22T11:33:49 cmac.h
-rw-r--r-- 1 mysql mysql  11223 2025-03-22T11:33:49 cmserr.h
-rw-r--r-- 1 mysql mysql  16379 2025-03-22T11:33:49 cms.h
-rw-r--r-- 1 mysql mysql   1212 2025-03-22T11:33:49 comperr.h
-rw-r--r-- 1 mysql mysql   1328 2025-03-22T11:33:49 comp.h
-rw-r--r-- 1 mysql mysql   1300 2025-03-22T11:33:49 conf_api.h
-rw-r--r-- 1 mysql mysql   3429 2025-03-22T11:33:49 conferr.h
-rw-r--r-- 1 mysql mysql   5601 2025-03-22T11:33:49 conf.h
-rw-r--r-- 1 mysql mysql   2261 2025-03-22T11:33:49 cryptoerr.h
-rw-r--r-- 1 mysql mysql  17239 2025-03-22T11:33:49 crypto.h
-rw-r--r-- 1 mysql mysql   3470 2025-03-22T11:33:49 cterr.h
-rw-r--r-- 1 mysql mysql  15872 2025-03-22T11:33:49 ct.h
-rw-r--r-- 1 mysql mysql   7627 2025-03-22T11:33:49 des.h
-rw-r--r-- 1 mysql mysql   4037 2025-03-22T11:33:49 dherr.h
-rw-r--r-- 1 mysql mysql  13505 2025-03-22T11:33:49 dh.h
-rw-r--r-- 1 mysql mysql   2972 2025-03-22T11:33:49 dsaerr.h
-rw-r--r-- 1 mysql mysql  10051 2025-03-22T11:33:49 dsa.h
-rw-r--r-- 1 mysql mysql   1578 2025-03-22T11:33:49 dtls1.h
-rw-r--r-- 1 mysql mysql    924 2025-03-22T11:33:49 ebcdic.h
-rw-r--r-- 1 mysql mysql    358 2025-03-22T11:33:49 ecdh.h
-rw-r--r-- 1 mysql mysql    358 2025-03-22T11:33:49 ecdsa.h
-rw-r--r-- 1 mysql mysql  15821 2025-03-22T11:33:49 ecerr.h
-rw-r--r-- 1 mysql mysql  63684 2025-03-22T11:33:49 ec.h
-rw-r--r-- 1 mysql mysql   5447 2025-03-22T11:33:49 engineerr.h
-rw-r--r-- 1 mysql mysql  34726 2025-03-22T11:33:49 engine.h
-rw-r--r-- 1 mysql mysql   8920 2025-03-22T11:33:49 e_os2.h
-rw-r--r-- 1 mysql mysql  11269 2025-03-22T11:33:49 err.h
-rw-r--r-- 1 mysql mysql  11453 2025-03-22T11:33:49 evperr.h
-rw-r--r-- 1 mysql mysql  76828 2025-03-22T11:33:49 evp.h
-rw-r--r-- 1 mysql mysql   1591 2025-03-22T11:33:49 hmac.h
-rw-r--r-- 1 mysql mysql   2099 2025-03-22T11:33:49 idea.h
-rw-r--r-- 1 mysql mysql   2122 2025-03-22T11:33:49 kdferr.h
-rw-r--r-- 1 mysql mysql   4326 2025-03-22T11:33:49 kdf.h
-rw-r--r-- 1 mysql mysql   9271 2025-03-22T11:33:49 lhash.h
-rw-r--r-- 1 mysql mysql   1054 2025-03-22T11:33:49 md2.h
-rw-r--r-- 1 mysql mysql   1322 2025-03-22T11:33:49 md4.h
-rw-r--r-- 1 mysql mysql   1320 2025-03-22T11:33:49 md5.h
-rw-r--r-- 1 mysql mysql   1053 2025-03-22T11:33:49 mdc2.h
-rw-r--r-- 1 mysql mysql  10478 2025-03-22T11:33:49 modes.h
-rw-r--r-- 1 mysql mysql   1316 2025-03-22T11:33:49 objectserr.h
-rw-r--r-- 1 mysql mysql   6633 2025-03-22T11:33:49 objects.h
-rw-r--r-- 1 mysql mysql 217522 2025-03-22T11:33:49 obj_mac.h
-rw-r--r-- 1 mysql mysql   3356 2025-03-22T11:33:49 ocsperr.h
-rw-r--r-- 1 mysql mysql  15305 2025-03-22T11:33:49 ocsp.h
-rw-r--r-- 1 mysql mysql   4728 2025-03-22T11:33:49 opensslconf.h
-rw-r--r-- 1 mysql mysql   4102 2025-03-22T11:33:49 opensslv.h
-rw-r--r-- 1 mysql mysql   6266 2025-03-22T11:33:49 ossl_typ.h
-rw-r--r-- 1 mysql mysql    415 2025-03-22T11:33:49 pem2.h
-rw-r--r-- 1 mysql mysql   5222 2025-03-22T11:33:49 pemerr.h
-rw-r--r-- 1 mysql mysql  15468 2025-03-22T11:33:49 pem.h
-rw-r--r-- 1 mysql mysql   3749 2025-03-22T11:33:49 pkcs12err.h
-rw-r--r-- 1 mysql mysql   9871 2025-03-22T11:33:49 pkcs12.h
-rw-r--r-- 1 mysql mysql   5110 2025-03-22T11:33:49 pkcs7err.h
-rw-r--r-- 1 mysql mysql  11590 2025-03-22T11:33:49 pkcs7.h
-rw-r--r-- 1 mysql mysql   4763 2025-03-22T11:33:49 rand_drbg.h
-rw-r--r-- 1 mysql mysql   4633 2025-03-22T11:33:49 randerr.h
-rw-r--r-- 1 mysql mysql   2213 2025-03-22T11:33:49 rand.h
-rw-r--r-- 1 mysql mysql   1534 2025-03-22T11:33:49 rc2.h
-rw-r--r-- 1 mysql mysql    825 2025-03-22T11:33:49 rc4.h
-rw-r--r-- 1 mysql mysql   1988 2025-03-22T11:33:49 rc5.h
-rw-r--r-- 1 mysql mysql   1243 2025-03-22T11:33:49 ripemd.h
-rw-r--r-- 1 mysql mysql   9075 2025-03-22T11:33:49 rsaerr.h
-rw-r--r-- 1 mysql mysql  22202 2025-03-22T11:33:49 rsa.h
-rw-r--r-- 1 mysql mysql   8139 2025-03-22T11:33:49 safestack.h
-rw-r--r-- 1 mysql mysql   3479 2025-03-22T11:33:49 seed.h
-rw-r--r-- 1 mysql mysql   3831 2025-03-22T11:33:49 sha.h
-rw-r--r-- 1 mysql mysql   3827 2025-03-22T11:33:49 srp.h
-rw-r--r-- 1 mysql mysql   1316 2025-03-22T11:33:49 srtp.h
-rw-r--r-- 1 mysql mysql    542 2025-03-22T11:33:49 ssl2.h
-rw-r--r-- 1 mysql mysql  14705 2025-03-22T11:33:49 ssl3.h
-rw-r--r-- 1 mysql mysql  46862 2025-03-22T11:33:49 sslerr.h
-rw-r--r-- 1 mysql mysql 111816 2025-03-22T11:33:49 ssl.h
-rw-r--r-- 1 mysql mysql   3095 2025-03-22T11:33:49 stack.h
-rw-r--r-- 1 mysql mysql   4399 2025-03-22T11:33:49 storeerr.h
-rw-r--r-- 1 mysql mysql  11199 2025-03-22T11:33:49 store.h
-rw-r--r-- 1 mysql mysql   1311 2025-03-22T11:33:49 symhacks.h
-rw-r--r-- 1 mysql mysql  72490 2025-03-22T11:33:49 tls1.h
-rw-r--r-- 1 mysql mysql   6746 2025-03-22T11:33:49 tserr.h
-rw-r--r-- 1 mysql mysql  22429 2025-03-22T11:33:49 ts.h
-rw-r--r-- 1 mysql mysql   1666 2025-03-22T11:33:49 txt_db.h
-rw-r--r-- 1 mysql mysql   2737 2025-03-22T11:33:49 uierr.h
-rw-r--r-- 1 mysql mysql  16052 2025-03-22T11:33:49 ui.h
-rw-r--r-- 1 mysql mysql   1377 2025-03-22T11:33:49 whrlpool.h
-rw-r--r-- 1 mysql mysql   6803 2025-03-22T11:33:49 x509err.h
-rw-r--r-- 1 mysql mysql  43326 2025-03-22T11:33:49 x509.h
-rw-r--r-- 1 mysql mysql   8901 2025-03-22T11:33:49 x509v3err.h
-rw-r--r-- 1 mysql mysql  33441 2025-03-22T11:33:49 x509v3.h
-rw-r--r-- 1 mysql mysql  32451 2025-03-22T11:33:49 x509_vfy.h
2025-03-23T16:04:59 [root@RDS-146 /glc/openssl/app/openssl-1.1.1w/include/openssl] #



```

#### ldconfig -p | grep libssl

```bash
2025-03-24T11:07:11 [root@RDS-146 /glc/expat/source/expat-2.6.0/expat] # ldconfig -p | grep libssl
	libssl3.so (libc6,x86-64) => /lib64/libssl3.so
	libssl.so.10 (libc6,x86-64) => /lib64/libssl.so.10
	libssl.so (libc6,x86-64) => /lib64/libssl.so

```



#### 3.4 源码安装

```bash
wget https://github.com/openssl/openssl/archive/refs/tags/OpenSSL_1_1_1w.tar.gz
tar -xzvf OpenSSL_1_1_1w.tar.gz
cd openssl-OpenSSL_1_1_1w
./config --prefix=/home/workspace/openssl/output/64
make -j4
make install
```

#### OpenSSL_1_1_1w.tar.gz

```bash
2025-03-27T22:27:13 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t] # ll
total 0
drwx------ 2 mysql mysql  37 Mar 27 22:21 bin
drwx------ 3 mysql mysql  21 Mar 27 22:21 include
drwx------ 4 mysql mysql 159 Mar 27 22:21 lib
drwx------ 4 mysql mysql  28 Mar 27 22:22 share
drwx------ 5 mysql mysql 140 Mar 27 22:21 ssl
2025-03-27T22:27:15 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t] # tree bin lib
bin
├── c_rehash
└── openssl
lib
├── engines-1.1
│   ├── afalg.so
│   ├── capi.so
│   └── padlock.so
├── libcrypto.a
├── libcrypto.so -> libcrypto.so.1.1
├── libcrypto.so.1.1
├── libssl.a
├── libssl.so -> libssl.so.1.1
├── libssl.so.1.1
└── pkgconfig
    ├── libcrypto.pc
    ├── libssl.pc
    └── openssl.pc

2 directories, 14 files
2025-03-27T22:27:20 [root@ky10arm-98 /glc/openssl/app/openssl-1.1.1t] # tree bin lib include
bin
├── c_rehash
└── openssl
lib
├── engines-1.1
│   ├── afalg.so
│   ├── capi.so
│   └── padlock.so
├── libcrypto.a
├── libcrypto.so -> libcrypto.so.1.1
├── libcrypto.so.1.1
├── libssl.a
├── libssl.so -> libssl.so.1.1
├── libssl.so.1.1
└── pkgconfig
    ├── libcrypto.pc
    ├── libssl.pc
    └── openssl.pc
include
└── openssl
    ├── aes.h
    ├── asn1err.h
    ├── asn1.h
    ├── asn1_mac.h
    ├── asn1t.h
    ├── asyncerr.h
    ├── async.h
    ├── bioerr.h
    ├── bio.h
    ├── blowfish.h
    ├── bnerr.h
    ├── bn.h
    ├── buffererr.h
    ├── buffer.h
    ├── camellia.h
    ├── cast.h
    ├── cmac.h
    ├── cmserr.h
    ├── cms.h
    ├── comperr.h
    ├── comp.h
    ├── conf_api.h
    ├── conferr.h
    ├── conf.h
    ├── cryptoerr.h
    ├── crypto.h
    ├── cterr.h
    ├── ct.h
    ├── des.h
    ├── dherr.h
    ├── dh.h
    ├── dsaerr.h
    ├── dsa.h
    ├── dtls1.h
    ├── ebcdic.h
    ├── ecdh.h
    ├── ecdsa.h
    ├── ecerr.h
    ├── ec.h
    ├── engineerr.h
    ├── engine.h
    ├── e_os2.h
    ├── err.h
    ├── evperr.h
    ├── evp.h
    ├── hmac.h
    ├── idea.h
    ├── kdferr.h
    ├── kdf.h
    ├── lhash.h
    ├── md2.h
    ├── md4.h
    ├── md5.h
    ├── mdc2.h
    ├── modes.h
    ├── objectserr.h
    ├── objects.h
    ├── obj_mac.h
    ├── ocsperr.h
    ├── ocsp.h
    ├── opensslconf.h
    ├── opensslv.h
    ├── ossl_typ.h
    ├── pem2.h
    ├── pemerr.h
    ├── pem.h
    ├── pkcs12err.h
    ├── pkcs12.h
    ├── pkcs7err.h
    ├── pkcs7.h
    ├── rand_drbg.h
    ├── randerr.h
    ├── rand.h
    ├── rc2.h
    ├── rc4.h
    ├── rc5.h
    ├── ripemd.h
    ├── rsaerr.h
    ├── rsa.h
    ├── safestack.h
    ├── seed.h
    ├── sha.h
    ├── srp.h
    ├── srtp.h
    ├── ssl2.h
    ├── ssl3.h
    ├── sslerr.h
    ├── ssl.h
    ├── stack.h
    ├── storeerr.h
    ├── store.h
    ├── symhacks.h
    ├── tls1.h
    ├── tserr.h
    ├── ts.h
    ├── txt_db.h
    ├── uierr.h
    ├── ui.h
    ├── whrlpool.h
    ├── x509err.h
    ├── x509.h
    ├── x509v3err.h
    ├── x509v3.h
    └── x509_vfy.h

3 directories, 118 files

```


