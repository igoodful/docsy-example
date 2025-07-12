---
title: partials
description: partials
date: 2023-11-04
weight: 3

---

## 自定义模板优先级更高
- project/layouts/partials/
- project/themes/your_theme/partials/
- project/layouts/partials/ 优先于 project/themes/your_theme/partials/
{{<note>}}
<!---->
比如project/layouts/partials/head.html和project/themes/your_theme/partials/head.html
，project/themes/your_theme/blog/baseof.html中引用head.html，那么它会引用project/layouts/partials/head.html
，而不是使用project/themes/your_theme/partials/head.html
```html
  <head>
    {{ partial "head.html" . }}
  </head>
```
{{</note>}}










