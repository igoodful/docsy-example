---
title: docsy
description: docsy
date: 2023-10-01
weight: 2000


---


{{< alert >}}

- [graphviz](https://graphviz.org/)
- [graphviz gitlab](https://gitlab.com/graphviz/graphviz.gitlab.io/-/tree/main)
- [graphviz 源码](https://gitlab.com/graphviz/graphviz)

{{< /alert >}}



### 将如下代码添加到`project/layouts/partials/head.html`文件最后
将会导致右侧tag和分类导航栏跟随外面的一起上下滑动，若文档很长，那么导致无法根据标题快速定位到指定位置，因此不要这样做

```html
<link href="/css/bootstrap-4.6.2.min.css" rel="stylesheet" />
```



