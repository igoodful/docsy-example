---
title: type cascade
description: 类型
date: 2023-11-04
weight: 3

---

## type

project/content/database/_index.md文件内容如下：
```yaml
---
title: 数据库
linkTitle: database
menu: {main: {weight: 200}}
weight: 200
cascade:
  - type: "docs"
---
```
那么project/content/database目录下面所有文档的类型就是docs，而不用每个文档都设置type属性









