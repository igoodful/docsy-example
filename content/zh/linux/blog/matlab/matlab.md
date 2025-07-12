---
title: 求和
date: 2023-09-30
weight: 200
description: >
  matlab 学习.
---
{{< alert >}}
matlab
{{< /alert >}}

##

{{< alert color="primary" title="符号求和函数：symsum(s,v,n,m)" >}}

- s表示一个级数的通项，是一个符号表达式。

- v是求和变量，v省略时使用系统的默认变量。

- n是求和变量v的初值

- m是求和变量v的末值。

{{< /alert >}}

```sql
>> syms n;
>> s1=symsum(1/(2*n+1)^2,n,1,inf)

s1 =

pi^2/8 - 1
```






























