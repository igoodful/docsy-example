---
title: yaml
description: taml
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [yaml](https://www.cnblogs.com/nf01/articles/15686885.html)
{{< /alert >}}


{{< alert  color="primary">}}

{{< /alert >}}


## 一、语法特点

大小写敏感
通过缩进表示层级关系
禁止使用tab缩进，只能使用空格键
缩进的空格数目不重要，只要相同层级左对齐即可
使用#表示注释

## 二、数据结构

YAML 支持的数据结构有三种
对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
纯量（scalars）：单个的、不可再分的值

## 三、数据类型


## 四、引号

双引号""：不会转义字符串里面的特殊字符，特殊字符作为本身想表示的意思。
name: "123\n123"
输出： 123 换行 123

单引号''：会将字符串里面的特殊字符转义为字符串处理
name: "123\n123"
输出： 123\n123
如果不加引号将会转义特殊字符，当成字符串处理


## 五、文本块





## 四、锚点与引用












