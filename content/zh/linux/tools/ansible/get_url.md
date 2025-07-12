---
title: get_url
description: get_url
date: 2023-10-12
weight: 60000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 1. 简介

会使用curl命令


## 2. 选项

{{< alert color="secondary" >}}


{{< /alert >}}


## 3. 案例



## 4. 问题

#### 4.1 SSL validation is not available in your version of python. You can use validate_certs=False, however this is unsafe and not recommended

- 现象
```bash
TASK [gmp : Download file from gmp_base_url_remote: from http://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.1.0.tar.bz2 to /glc/tmpdir/gmp-6.1.0.tar.bz2] **************************************************************************************************************************************************
fatal: [node2]: FAILED! => changed=false
  msg: SSL validation is not available in your version of python. You can use validate_certs=False, however this is unsafe and not recommended
  status: -1
  url: http://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.1.0.tar.bz2

PLAY RECAP **********************************************************************************************************************************************************************************************************************************************************************************************
node2                      : ok=13   changed=4    unreachable=0    failed=1    skipped=7    rescued=0    ignored=1
```

- 检查curl命令和依赖的动态库
```bash
2025-03-28T09:22:49 [mysql@ky10arm-98 /home/mysql] $ curl
curl: relocation error: /lib64/libcurl.so.4: symbol SSLv3_client_method version OPENSSL_1_1_0 not defined in file libssl.so.1.1 with link time reference
2025-03-28T09:22:52 [mysql@ky10arm-98 /home/mysql] $ ldd /usr/bin/curl
	linux-vdso.so.1 (0x0000fffc96020000)
	libcurl.so.4 => /lib64/libcurl.so.4 (0x0000fffc95f00000)
	libmetalink.so.3 => /lib64/libmetalink.so.3 (0x0000fffc95ed0000)
	libssl.so.1.1 => /glc/openssl/app/openssl-1.1.1t/lib/libssl.so.1.1 (0x0000fffc95e20000)
	libcrypto.so.1.1 => /glc/openssl/app/openssl-1.1.1t/lib/libcrypto.so.1.1 (0x0000fffc95b40000)
	libz.so.1 => /lib64/libz.so.1 (0x0000fffc95b00000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffc95ac0000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffc95930000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffc96030000)
	libnghttp2.so.14 => /lib64/libnghttp2.so.14 (0x0000fffc958e0000)
	libidn2.so.0 => /lib64/libidn2.so.0 (0x0000fffc958a0000)
	libssh.so.4 => /lib64/libssh.so.4 (0x0000fffc95800000)
	libpsl.so.5 => /lib64/libpsl.so.5 (0x0000fffc957d0000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x0000fffc95760000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x0000fffc95660000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x0000fffc95620000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x0000fffc955f0000)
	libldap-2.4.so.2 => /lib64/libldap-2.4.so.2 (0x0000fffc95580000)
	liblber-2.4.so.2 => /lib64/liblber-2.4.so.2 (0x0000fffc95550000)
	libbrotlidec.so.1 => /lib64/libbrotlidec.so.1 (0x0000fffc95520000)
	libexpat.so.1 => /usr/lib64/libexpat.so.1 (0x0000fffc954d0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffc954a0000)
	libunistring.so.2 => /lib64/libunistring.so.2 (0x0000fffc95300000)
	librt.so.1 => /lib64/librt.so.1 (0x0000fffc952d0000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x0000fffc952a0000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x0000fffc95270000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x0000fffc95230000)
	libsasl2.so.3 => /lib64/libsasl2.so.3 (0x0000fffc951f0000)
	libm.so.6 => /lib64/libm.so.6 (0x0000fffc95120000)
	libbrotlicommon.so.1 => /lib64/libbrotlicommon.so.1 (0x0000fffc950e0000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x0000fffc95090000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x0000fffc95030000)
	libsecurity.so.0 => /lib64/libsecurity.so.0 (0x0000fffc95000000)
	libpcre2-8.so.0 => /lib64/libpcre2-8.so.0 (0x0000fffc94f60000)

```

- 恢复openssl的动态库
```bash
export PATH="/glc/openssl/app/openssl-1.1.1t/bin:$PATH"
export C_INCLUDE_PATH="/glc/openssl/app/openssl-1.1.1t/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/openssl/app/openssl-1.1.1t/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/openssl/app/openssl-1.1.1t/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/openssl/app/openssl-1.1.1t/lib:$LD_LIBRARY_PATH"

```

解决：去掉这个安装的openssl路径





## 5. 参考

```bash
# msg
ansible localhost -m debug -a 'msg="hello world"'

# msg
ansible localhost -e 'str=world' -m debug -a 'msg="hello {{str}}"'

# var
ansible localhost -e 'str="hello world"' -m debug -a 'var=str'
```














