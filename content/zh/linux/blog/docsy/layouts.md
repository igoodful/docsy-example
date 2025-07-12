---
title: layouts
description: 模板
date: 2023-11-04
weight: 3

---
- https://hugo.aiaide.com/
{{<pageinfo>}}
页面 = 文章 + 模板


{{</pageinfo>}}
## 首页
-

- 首页添加一个markdown文件位置：hugo.toml中contentDir变量所指的目录下的_index.md，这个不是必须的.

```sql
如果没有给首页添加模板, 首页使用内容页的模板


```

{{<note>}}
<!---->
页面 = 文章 + 模板

整体上分为两类: single(单页面 - page) 和 list(列表页 - home, section, taxonomyTerm, taxonomy).
{{</note>}}



页面和模板的对应关系
页面和模板的应对关系是根据页面的一系列的属性决定的, 这些属性有: Kind, Output Format, Language, Layout, Type, Section. 他们不是同时起作用, 其中kind, layout, type, section用的比较多.

- kind: 用于确定页面的类型, 单页面使用single.html为默认模板页, 列表页使用list.html为默认模板页, 值不能被修改
- section: 用于确定section tree下面的文章的模板. section tree的结构是由content目录结构生成的, 不能被修改, content目录下的一级目录自动成为root section, 二级及以下的目录, 需要在目录下添加_index.md文件才能成为section tree的一部分. 如果页面不在section tree下section的值为空
- type: 可以在Front Matter中设置, 用户指定模板的类型. 如果没设定type的值, type的值等于section的值 或 等于page(section为空的时候)
- layout: 可以在Front Matter中设置, 用户指定具体的模板名称.