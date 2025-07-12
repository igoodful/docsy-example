---
title:  head.html
description:  head.html
date: 2023-10-30
weight: 3000
viz: true
---

{{< note >}}


{{< /note >}}


```viz-dot
digraph "PFS_builtin_memory_class" {
        rankdir=LR;
        edge [
                fontsize="11"
                ];
        node [
                shape = octagon;
                fontsize = "11"
                ];
        "footer" -> "center.html";
        "footer" -> "copyright.html";
        "footer" -> "left.html";
        "footer" -> "links.html";
        "footer" -> "right.html";
}
```

## head.html


```python
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
{{ range .AlternativeOutputFormats -}}
<link rel="{{ .Rel }}" type="{{ .MediaType.Type }}" href="{{ .Permalink | safeURL }}">
{{ end -}}

{{ $outputFormat := partial "outputformat.html" . -}}
{{ if and hugo.IsProduction (ne $outputFormat "print") -}}
<meta name="robots" content="index, follow">
{{ else -}}
<meta name="robots" content="noindex, nofollow">
{{ end -}}

{{ partialCached "favicons.html" . }}
<title>
  {{- if .IsHome -}}
    {{ .Site.Title -}}
  {{ else -}}
    {{ with .Title }}{{ . }} | {{ end -}}
    {{ .Site.Title -}}
  {{ end -}}
</title>
<meta name="description" content="{{ template "partials/page-description.html" . }}">
{{ template "_internal/opengraph.html" . -}}
{{ template "_internal/schema.html" . -}}
{{ template "_internal/twitter_cards.html" . -}}
{{ partialCached "head-css.html" . "head-css-cache-key" -}}
<script src='{{ "js/jquery-3.7.1.min.js" | relURL }}' > </script>
{{ if .Site.Params.offlineSearch -}}
<script src='{{ "js/lunr-2.3.9.min.js" | relURL }}'></script>
{{ end -}}

{{ if .Site.Params.prism_syntax_highlighting -}}
<link rel="stylesheet" href='{{ "css/prism.css" | relURL }}'/>
{{ end -}}

{{ template "algolia/head" . -}}

{{ partial "hooks/head-end.html" . -}}

{{/* To comply with GDPR, cookie consent scripts places in head-end must execute before Google Analytics is enabled */ -}}
{{ if hugo.IsProduction -}}
  {{ template "_internal/google_analytics.html" . -}}
{{ end -}}

{{ define "algolia/head" -}}

{{ if and .Site.Params.search (isset .Site.Params.search "algolia") -}}
<link rel="stylesheet" href='{{ "css/docsearch-3.6.0.css" | relURL }}' />
{{ end -}}

{{ if ne .Site.Params.algolia_docsearch nil -}}
{{ warnf `Config 'params.algolia_docsearch' is deprecated: use 'params.search.algolia'
      For details, see https://www.docsy.dev/docs/adding-content/search/#algolia-docsearch.` -}}
{{ end -}}

{{ end -}}


```



