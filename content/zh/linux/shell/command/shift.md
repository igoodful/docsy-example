---
title: shift
description: shift
date: 2025-02-19
weight: 300000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="shift参数详解" color="secondary">}}

- `shift N`：左移N个参数，从而消去N个参数，前N位参数都会被销毁
- `shift`：将参数列表向左移动一位，即移除第一个参数。

{{< /alert >}}



## 一、原理

左移一个参数，从而消去一个参数

| 选项 | 作用 | 示例 |
|:-----|:-----|:-----|
| `-l` |      | `-l` |



## 案例分享

### 1. shift.sh

```bash
#!/bin/bash
while [ $# != 0 ]; do
	echo "第一个参数为：$1,参数个数为：$#"
	shift
done

```


```bash
2025-03-13T14:27:58 [igoodfu@48 /db/storage/guolicheng/scripts] $ sh shift.sh a b c d e f
第一个参数为：a,参数个数为：6
第一个参数为：b,参数个数为：5
第一个参数为：c,参数个数为：4
第一个参数为：d,参数个数为：3
第一个参数为：e,参数个数为：2
第一个参数为：f,参数个数为：1

```



## 2. zookeeper的启动脚本

zookeeper-3.6.2/bin/zkEnv.sh
```bash
if [ $# -gt 1 ]; then
	if [ "--config" = "$1" ]; then
		shift
		confdir=$1
		shift
		ZOOCFGDIR=$confdir
	fi
fi
```






