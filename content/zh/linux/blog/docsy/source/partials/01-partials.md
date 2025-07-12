---
title: partials
description: partials
date: 2023-10-30
weight: 1000
viz: true
---




#### 部分模板查找顺序

部分模板——就像单页模板和列表页模板一样——有一种特定的查找顺序。然而，部分模板更简单，因为Hugo只会在两个位置检查：
1. layouts/partials/xxx.html
2. themes/<THEME>/layouts/partials/xxx.html
3. layouts/partials目录中。为了更好地组织，您还可以在partials目录中创建多个子目录



```viz-dot
digraph "PFS_builtin_memory_class" {
        rankdir=LR;
        edge [
                fontsize="11"
                ];
        node [
                shape = rect;
                fontsize = "11"
                ];
        "partials" -> "breadcrumb.html";
        "partials" -> "community_links.html";
        "partials" -> "disqus-comment.html";
        "partials" -> "favicons.html";
        "partials" -> "featured-image.html";
        "partials" -> "feedback.html";
        "partials" -> "footer.html";
        "partials" -> "head-css.html";
        "partials" -> "navbar-lang-selector.html";
        "partials" -> "navbar-version-selector.html";
        "partials" -> "navbar.html";
        "partials" -> "outputformat.html";
        "partials" -> "page-description.html";
        "partials" -> "page-meta-lastmod.html";
        "partials" -> "page-meta-links.html";
        "partials" -> "pager.html";
        "partials" -> "reading-time.html";
        "partials" -> "scripts.html";
        "partials" -> "search-input.html";
        "partials" -> "section-index.html";
        "partials" -> "sidebar-tree.html";
        "partials" -> "sidebar.html";
        "partials" -> "taxonomy_terms_article.html";
        "partials" -> "taxonomy_terms_article_wrapper.html";
        "partials" -> "taxonomy_terms_cloud.html";
        "partials" -> "taxonomy_terms_clouds.html";
        "partials" -> "theme-toggler.html";
        "partials" -> "toc.html";
        "partials" -> "version-banner.html";
        "partials" -> "footer";
        "partials" -> "hooks";
        "partials" -> "print";
        "partials" -> "scripts";

        "footer" -> "center.html";
        "footer" -> "copyright.html";
        "footer" -> "left.html";
        "footer" -> "links.html";
        "footer" -> "right.html";

        "hooks" -> "body-end.html";
        "hooks" -> "head-end.html";

        "print" -> "content-blog.html";
        "print" -> "content.html";
        "print" -> "page-heading.html";
        "print" -> "render.html";
        "print" -> "toc-li-blog.html";

        "scripts" -> "mermaid.html";
}
```


#### 内联部分模板

您还可以在模板中内联定义部分模板。但请记住，模板命名空间是全局的，因此您需要确保名称是唯一的，以避免冲突。

```python
Value: {{ partial "my-inline-partial.html" . }}

{{ define "partials/my-inline-partial.html" }}
{{ $value := 32 }}
{{ return $value }}
{{ end }}

```


## breadcrumb.html

```python
{{ $isSingle := true -}}
{{ with .Parent -}}
  {{ $isSingle = .IsHome -}}
{{ end -}}
<nav aria-label="breadcrumb" class="td-breadcrumbs
    {{- if $isSingle }} td-breadcrumbs__single {{- end }}">
  <ol class="breadcrumb">
    {{- template "breadcrumbnav" (dict "p1" . "p2" .) }}
  </ol>
</nav>

{{- define "breadcrumbnav" -}}
  {{ if .p1.Parent -}}
    {{ if not .p1.Parent.IsHome -}}
      {{ template "breadcrumbnav" (dict "p1" .p1.Parent "p2" .p2 )  -}}
    {{ end -}}
  {{ else if not .p1.IsHome -}}
    {{ template "breadcrumbnav" (dict "p1" .p1.Site.Home "p2" .p2 )  -}}
  {{ end -}}
  {{ $isActive :=  eq .p1 .p2 }}
  <li class="breadcrumb-item{{ if $isActive }} active{{ end }}"
      {{- if $isActive }} aria-current="page"{{ end }}>
    {{ if $isActive -}}
      {{ .p1.LinkTitle -}}
    {{ else -}}
      <a href="{{ .p1.RelPermalink }}">{{ .p1.LinkTitle }}</a>
    {{- end -}}
  </li>
{{- end -}}

```









## version-banner.html

```python
<!-- Check the variable that indicates whether this is an archived doc set.
  If yes, display a banner. -->
  {{ if .Site.Params.archived_version }}
  {{ $color := "primary" }}
  {{ $latest_version := .Site.Params.url_latest_version }}
  {{ $current_version := .Site.Params.version }}
  <div class="pageinfo pageinfo-{{ $color }}">
    {{ with $current_version }}<p>Version {{ . | markdownify }} of the
      documentation is no longer actively maintained. The site that you are
      currently viewing is an archived snapshot.
      {{ with $latest_version }}For up-to-date documentation, see the
        <a href="{{ $latest_version | safeURL }}" target="_blank">latest version</a>.</p>
      {{ end }}
    {{ end }}
  </div>
{{ end }}


```






