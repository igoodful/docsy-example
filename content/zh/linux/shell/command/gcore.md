---
title: gcore
description: gcore
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

| 选项 | 作用                   | 示例 |
|:-----|:---------------------|:-----|
| `g`  | 返回文件开头           |      |
| `G`  | 返回文件结尾           |      |
| `n`  | 动到模式的下一次出现   |      |
| `N`  | 移动到模式的上一次出现 |      |
| `/`  | 正则表达式             |      |
| `?`  | 文件中向后搜索模式     |      |
| `j`  | 向前移动一行           |      |
| `k`  | 向后移动一行           |      |

## 3. 案例

```bash
gcore -o filename $pid

# 6618
gcore -o filename 6618
gdb /data/app/mysql-8.0.32/bin/mysqld filename.6618


```

## 4. 问题



## 5. 参考




