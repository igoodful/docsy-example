---
title: ldd
description: ldd、readelf、objdump、nm、rpm、yum
date: 2025-03-18
weight: 20
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}




在 CentOS 上，查看一个动态库（`.so` 文件）的依赖包可以使用以下工具和方法：

---

### **1. 使用 `ldd` 命令**
`ldd` 是 Linux 上最常用的工具，用于列出动态库或可执行文件的依赖关系。

#### **语法**
```bash
ldd /path/to/library.so
```

#### **示例**
```bash
ldd /usr/lib64/libcurl.so.4
```

#### **输出示例**
```plaintext
    linux-vdso.so.1 =>  (0x00007ffd5b5f0000)
    libssl.so.10 => /lib64/libssl.so.10 (0x00007f8c1b3f0000)
    libcrypto.so.10 => /lib64/libcrypto.so.10 (0x00007f8c1af80000)
    libz.so.1 => /lib64/libz.so.1 (0x00007f8c1ad60000)
    libc.so.6 => /lib64/libc.so.6 (0x00007f8c1a990000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f8c1b810000)
```

- **`=>` 左边**：依赖的库名称。
- **`=>` 右边**：依赖库的实际路径。
- **`not found`**：如果某个依赖库未找到，会显示 `not found`。

---

### **2. 使用 `readelf` 命令**
`readelf` 是一个更强大的工具，可以查看 ELF 格式文件（包括动态库）的详细信息。

#### **语法**
```bash
readelf -d /path/to/library.so
```

#### **示例**
```bash
readelf -d /usr/lib64/libcurl.so.4
```

#### **输出示例**
```plaintext
Dynamic section at offset 0x3e000 contains 29 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.10]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.10]
 0x0000000000000001 (NEEDED)             Shared library: [libz.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
```

- **`NEEDED`**：表示依赖的库。

---

### **3. 使用 `objdump` 命令**
`objdump` 是另一个强大的工具，可以查看二进制文件的详细信息。

#### **语法**
```bash
objdump -p /path/to/library.so
```

#### **示例**
```bash
objdump -p /usr/lib64/libcurl.so.4
```

#### **输出示例**
```plaintext
Dynamic Section:
  NEEDED               libssl.so.10
  NEEDED               libcrypto.so.10
  NEEDED               libz.so.1
  NEEDED               libc.so.6
```

- **`NEEDED`**：表示依赖的库。

---

### **4. 使用 `nm` 命令**
`nm` 可以列出动态库中的符号，间接查看依赖关系。

#### **语法**
```bash
nm -D /path/to/library.so
```

#### **示例**
```bash
nm -D /usr/lib64/libcurl.so.4
```

#### **输出示例**
```plaintext
0000000000012345 T curl_easy_cleanup
0000000000012346 T curl_easy_init
                 U SSL_connect
                 U SSL_library_init
                 U ZLIB_VERSION
```

- **`U`**：表示未定义的符号，通常是依赖的外部库。

---

### **5. 使用 `rpm` 命令（适用于 RPM 包）**
如果动态库是通过 RPM 包安装的，可以使用 `rpm` 查看其依赖关系。

#### **语法**
```bash
rpm -q --requires <package_name>
```

#### **示例**
```bash
rpm -q --requires libcurl
```

#### **输出示例**
```plaintext
libc.so.6()(64bit)
libcrypto.so.10()(64bit)
libssl.so.10()(64bit)
libz.so.1()(64bit)
```

---

### **6. 使用 `yum` 命令（适用于查找未安装的依赖）**
如果动态库的依赖未安装，可以使用 `yum` 查找并安装。

#### **语法**
```bash
yum provides /path/to/missing_library.so
```

#### **示例**
```bash
yum provides */libssl.so.10
```

#### **输出示例**
```plaintext
openssl-libs-1.0.2k-24.el7.x86_64 : Files for general cryptographic purposes
Repo        : base
Matched from:
Filename    : /usr/lib64/libssl.so.10
```

---

### **总结**
| **工具**  | **特点**                     | **适用场景**                 |
| --------- | ---------------------------- | ---------------------------- |
| `ldd`     | 简单直观，直接显示依赖库路径 | 快速查看动态库依赖           |
| `readelf` | 显示 ELF 文件的详细信息      | 需要深入了解动态库结构时使用 |
| `objdump` | 显示二进制文件的详细信息     | 需要分析动态库内容时使用     |
| `nm`      | 列出符号表，间接查看依赖     | 需要分析动态库符号时使用     |
| `rpm`     | 查看 RPM 包的依赖关系        | 动态库通过 RPM 包安装时使用  |
| `yum`     | 查找未安装的依赖库           | 动态库依赖缺失时使用         |

通过以上工具，可以全面分析动态库的依赖关系，解决运行时依赖缺失的问题。



## Linux下找不到so文件的解决办法
```bash
[root@localhost tengine]# sbin/nginx
sbin/nginx: error while loading shared libraries: libjemalloc.so.2:
cannot open shared object file: No such file or directory

```


### 1. 首先找找系统中有没有这个文件
```bash
[root@localhost tengine]# sudo find / -name libjemalloc.so.2
/usr/local/lib/libjemalloc.so.2
```

### 2. 若有这个文件：

#### 2.1 添加/etc/ld.so.conf.d/
```bash
[root@localhost tengine]# cat /etc/ld.so.conf.d/libjemalloc.conf
/usr/jemalloc/lib

```

#### 2.2 让文件生效
```bash
[root@localhost tengine]# sudo /sbin/ldconfig

```

#### 2.3 查看可执行文件所依赖的文件

```bash
[root@localhost ld.so.conf.d]# ldd /usr/local/tengine/sbin/nginx
    linux-vdso.so.1 =>  (0x00007ffc9fd66000)
    libpthread.so.0 => /lib64/libpthread.so.0 (0x00007ff1c5f56000)
    libdl.so.2 => /lib64/libdl.so.2 (0x00007ff1c5d52000)
    libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007ff1c5b1a000)
    libjemalloc.so.2 => /usr/jemalloc/lib/libjemalloc.so.2 (0x00007ff1c58cb000)
    libc.so.6 => /lib64/libc.so.6 (0x00007ff1c550a000)
    /lib64/ld-linux-x86-64.so.2 (0x00007ff1c6187000)
    libfreebl3.so => /lib64/libfreebl3.so (0x00007ff1c5306000)
```


### 3. 若没有这个文件，则yum或源码安装

```bash


```


## 参考



- [Linux下找不到so文件的解决办法](https://blog.csdn.net/rznice/article/details/50857220)




