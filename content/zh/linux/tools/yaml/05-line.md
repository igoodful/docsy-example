---
title: 五、换行
description: line
date: 2023-10-31
weight: 500


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
换行主要使用 `|` 和 `>`

`+ -`一般用于与`|`和`>`组合使用

保留字符末尾的换行使用+，删除字符末尾的换行使用-

- \| 保留行尾换行符

- \> 将行尾换行符替换成空格

- \+ 保留字符末尾的换行

- \- 删除字符末尾的换行使用


{{< /alert >}}


{{< alert  color="primary">}}

{{< /alert >}}



## 一、`|`
`|`保留行尾换行符

```yaml
w: |
   Hello
   World

```

输出：
```
{'w': 'Hello\nWorld\n'}
```

## 二、`|+`
`|+`保留行尾及字符末尾的换行符

```yaml
w: |+
   Hello
   World

```

输出：
```
{'w': 'Hello\nWorld\n'}
```

## 三、`|-`
`|-`保留行尾换行符，但不保留字符末尾的换行符

```yaml
w: |-
   Hello
   World

```

输出：
```
{'w': 'Hello\nWorld'}
```



## 四、`>`
```yaml
w: |
   Hello
   World

```
输出：

```
{'w': 'Hello World\n'}
```

## 五、`>+`
`>+`将行尾换行符替换成空格，保留字符末尾的换行符

```yaml
w: >+
   Hello
   World

```

输出：

```
{'w': 'Hello World\n'}
```

## 六、`>-`

`>-`将行尾换行符替换成空格，不保留字符末尾的换行符

```yaml
w: >-
   Hello
   World

```

输出：

```
{'w': 'Hello World'}
```



