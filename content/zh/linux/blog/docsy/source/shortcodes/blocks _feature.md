---
title: blocks/feature
description: blocks/feature
date: 2023-10-30
weight: 10000


viz: true
---
{{< alert >}}

- [https://github.com/martignoni/hugo-notice.git](https://github.com/martignoni/hugo-notice.git)
- [https://www.docsy.dev/docs/examples/](https://www.docsy.dev/docs/examples/)

{{< /alert >}}




```python
{{ $icon := .Get "icon" | default "fa-lightbulb" -}}
<div class="col-lg-4 mb-5 mb-lg-0 text-center">
<div class="mb-4 h1">
  <i class="{{ if not (or (hasPrefix $icon "fas ") (hasPrefix $icon "fab ")) }}fas {{ end }}{{ $icon }}"></i>
</div>
<h4 class="h3">
  {{- .Get "title" | markdownify -}}
</h4>
<div class="mb-0">
{{ .Inner }}
</div>
{{ with .Get "url" }}<p><a href="{{ . }}">{{ with $.Get "url_text" }}{{ . }}{{ else }}{{ T "ui_read_more" }}{{ end }}</a></p>{{ end }}
</div>

```









