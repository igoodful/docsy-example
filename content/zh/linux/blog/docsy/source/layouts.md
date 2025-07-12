---
title: layouts
description: docsy/layouts
date: 2024-06-10
weight: 2000


viz: true
---



## 结构
```viz-dot
digraph "docsy/layouts" {
        rankdir=LR;
        edge [
                fontsize="11"
                ];
        node [
                shape = rect;
                fontsize = "11"
                ];
        "docsy/layouts" -> "404.html" ;
        "docsy/layouts" -> "home.html" ;
        "docsy/layouts" -> "_default";
        "docsy/layouts" -> "_internal";
        "docsy/layouts" -> "blog";
        "docsy/layouts" -> "community";
        "docsy/layouts" -> "docs" ;
        "docsy/layouts" -> "swagger" ;
        "docsy/layouts" -> "shortcodes" ;
        "docsy/layouts" -> "partials" ;

        "_default" -> "_default/baseof.html";
        "_default" -> "_default/content.html";
        "_default" -> "_default/list.html";
        "_default" -> "_default/search.html";
        "_default" -> "_default/single.html";
        "_default" -> "_default/taxonomy.html";
        "_default" -> "_default/terms.html";
        "_default" -> "_default/_markup";

        "_internal" -> "google_analytics.html";

        "blog" -> "blog/baseof.html";
        "blog" -> "blog/list.html";
        "blog" -> "blog/single.html";
        "blog" -> "blog/content.html";
        "blog" -> "blog/baseof.print.html";
        "blog" -> "blog/section.print.html";

        "community" -> "community/list.html";

        "docs" -> "docs/baseof.html";
        "docs" -> "docs/list.html"
        "docs" -> "docs/single.html";
        "docs" -> "docs/baseof.print.html";
        "docs" -> "docs/list.print.html";

        "shortcodes" -> "alert.html";
        "shortcodes" -> "card.html";
        "shortcodes" -> "cardpane.html";
        "shortcodes" -> "conditional-text.html";
        "shortcodes" -> "figure.html";
        "shortcodes" -> "iframe.html";
        "shortcodes" -> "imgproc.html";
        "shortcodes" -> "pageinfo.html";
        "shortcodes" -> "readfile.html";
        "shortcodes" -> "redoc.html";
        "shortcodes" -> "swaggerui.html";
        "shortcodes" -> "tab.html";
        "shortcodes" -> "tabpane.html";

        "shortcodes" -> "blocks";
        "blocks" -> "cover.html";
        "blocks" -> "feature.html";
        "blocks" -> "lead.html";
        "blocks" -> "link-down.html";
        "blocks" -> "section.html";

        "swagger" -> "swagger/baseof.html";
        "swagger" -> "swagger/list.html";
        "swagger" -> "swagger/single.html";


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


## 404.html

```python
{{ define "main" -}}
<div class="td-content">
  <h1>Not found</h1>
  <p>Oops! This page doesn't exist. Try going back to the <a href="{{ "" | relURL }}">home page</a>.</p>
</div>
{{- end }}
```



## home.html

```python
{{ define "main" }}
{{ with .Content }}
{{ . }}
{{ end }}
{{ end }}
```







