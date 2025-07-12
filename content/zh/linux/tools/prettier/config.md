---
title: config
description: ".prettierrc"
date: 2025-03-21
weight: 2000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


{{< /alert >}}


{{< alert  color="primary">}}

{{< /alert >}}


## 一、配置

- `cat .prettierrc`

```json
{
  "singleQuote": true,
  "printWidth": 120,
  "tabWidth": 4,
  "bracketSpacing": true,
}


```


## 二、格式化

```bash
 find ./ -name "*.yml"|xargs  prettier --write


```



