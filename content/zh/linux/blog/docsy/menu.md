---
title: menu
description: menu
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

```toml
# 主导航菜单
name="首页"
url="/"
weight="1"
[[menu.main]]
name="博文"
url="/post"
weight="2"
[[menu.main]]
name="新闻"
url="/news"
weight="3"
[[menu.main]]
name="关于"
url="/about"
weight="4"

# 页脚导航菜单
[[menu.foot]]
name="网站首页"
url="/"
weight="4"
[[menu.foot]]
name="博文集合"
url="/post"
weight="3"
[[menu.foot]]
name="新闻列表"
url="/news"
weight="2"
[[menu.foot]]
name="关于我们"
url="/about"
weight="1"
```