---
title: seq
description: 生成序列
date: 2023-10-31
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}



{{< alert title="seq参数详解" color="secondary">}}

- 指定分割符：`-s`
  - 默认使用：\n
  - `seq -s + 1 2 10`

- 指定输出数字同宽：`-w`
  - `seq -w 10`
- 指定宽度：`-f`
  - 默认格式为%g，不能和-f一起用，输出是同宽的
  - `%02g`表示指定位宽为两位，数字位数不足用0补位，这种写法跟上述提到的-w选项类似，只不过-w是以指定的最大值的位数为最大位数，而-f选项可以直接指定位数
  - `seq -f '%03g' 10`
  - `seq -f 'dir_%3g' 10`

{{< /alert >}}





```bash
# seq 1 3 11
1
4
7
10

# seq 10 -1 1
10
9
8
7
6
5
4
3
2
1

# seq  1 0.5 3
1.0
1.5
2.0
2.5
3.0

# seq -f "%.2f" 1 0.5 3
1.00
1.50
2.00
2.50
3.00

```
- `%.2f`显示2位小数



```bash
# seq -s "`echo -e "\t"`" 6
1	2	3	4	5	6


```


```bash
# seq -w 10
01
02
03
04
05
06
07
08
09
10

```




```bash
# seq -f '%03g' 10
001
002
003
004
005
006
007
008
009
010
[root@dev /root/tmp/test] TTY 0 FROM 172.17.120.253
# seq -f '%3g' 10
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10

```



### 创建大量文件或目录

```bash
seq -f '%04g' 10 |xargs mkdir -p
```









