---
title: install
description: install
date: 2025-02-19
weight: 300000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert title="curl参数详解" color="secondary">}}

{{< /alert >}}



## 1. 简介


## 2. 选项

| 选项 | 作用                                           | 示例                                         |
| :--- | :--------------------------------------------- | :------------------------------------------- |
| `-D` | 创建DEST前的所有主目录，然后将SOURCE复制至DEST | `install -D hello /opt/bin/hello`            |
| `-d` | 创建目录                                       | `install -d /opt/22 /opt/33`                 |
| `-o` | 所有者                                         | `install -m755 -o www -g www -d /etc/my.cnf` |
| `-g` | 组                                             | `install -m755 -o www -g www -d /etc/my.cnf` |
| `-m` | 模式                                           | `install -m755 -o www -g www -d /etc/my.cnf` |



## 3. 案例

```bash
# cp libfoo.so /usr/local/lib
install libfoo.so /usr/local/lib

# mkdir -p /opt/bin && cp hello /opt/bin/hello
install -D hello /opt/bin/hello

# mkdir -p /opt/22 /opt/33
install -d /opt/22 /opt/33

# chown www:www /etc/my.cnf && chmod 755 /etc/my.cnf
install -m755 -o www -g www -d /etc/my.cnf

```

## 4. 问题

- 有的软件不能在源码中直接编译，比如glibc
- 有的软件只能在源码中直接编译，比如socat源码，若在其他路径编译，可能会报无法找到文件的错误



## 5. 参考




