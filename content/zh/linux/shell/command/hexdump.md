---
title: hexdump
description: hexdump
date: 2025-04-01
weight: 30000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}


{{</alert>}}



## 选项


格式说明符由 % 字符开始，后跟一个或多个字符，用于指定输出的格式。常用的格式说明符包括：

- `-n` length 只格式化输入文件的前length个字节。
- `-C` 输出规范的十六进制和ASCII码。
- `-b` 单字节八进制显示。
- `-c` 单字节字符显示。
- `-d` 双字节十进制显示。
- `-o` 双字节八进制显示。
- `-x` 双字节十六进制显示。
- `-s` 从偏移量开始输出。
- `-e` 指定格式字符串，格式字符串包含在一对单引号中，格式字符串形如：'a/b "format1" "format2"'。





## 使用

-
```bash
hexdump -C   a.txt

```

```bash
hexdump -x   a.txt
```



#### hexdump

```bash
[mysql@RDS-146 /home/mysql] $ cat a.log
ABC\n
1234

[mysql@RDS-146 /home/mysql] $ hexdump -C a.log
00000000  41 42 43 5c 6e 0a 31 32  33 34 0a 0a              |ABC\n.1234..|
0000000c
[mysql@RDS-146 /home/mysql] $ hexdump -c a.log
0000000   A   B   C   \   n  \n   1   2   3   4  \n  \n
000000c
2025-04-28T17:46:18 [mysql@RDS-146 /home/mysql] $

```










