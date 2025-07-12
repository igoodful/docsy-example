---
title: notice
description: notice
date: 2023-10-30
weight: 10000


viz: true
---
{{< alert >}}

- [https://github.com/martignoni/hugo-notice.git](https://github.com/martignoni/hugo-notice.git)
- [https://www.docsy.dev/docs/examples/](https://www.docsy.dev/docs/examples/)

{{< /alert >}}

{{< progress value="15;30;20;10;20" >}}

===============================
{{< notice warning >}}
警告：提醒问题和危险
{{< /notice >}}



{{< notice note >}}
注意：重要信息和提示
{{< /notice >}}

{{< notice info >}}
信息：仅供参考，背景信息和额外的细节
{{< /notice >}}





{{< notice tip >}}
提示：实用的技巧和建议
{{< /notice >}}


## alert
```python
{{ $_hugo_config := `{ "version": 1 }` }}
{{ $color := .Get "color" | default "primary" }}
<div class="alert alert-{{ $color }}" role="alert">
{{ with .Get "title" }}<h4 class="alert-heading">{{ . | safeHTML }}</h4>{{ end }}
{{ if eq .Page.File.Ext "md" }}
    {{ .Inner | markdownify }}
{{ else }}
    {{ .Inner | htmlUnescape | safeHTML }}
{{ end }}
</div>
```

{{< alert title="主要颜色" color="primary">}}
primary
{{< /alert >}}

{{< alert title="次要颜色" color="secondary" >}}
secondary
{{< /alert >}}




{{< alert title="注意" color="success">}}
success
{{< /alert >}}


{{< alert title="危险" color="danger">}}
danger
{{< /alert >}}


{{< alert title="警告" color="warning">}}
warning
{{< /alert >}}

{{< alert title="信息" color="info">}}
info
{{< /alert >}}


{{< alert title="light" color="light">}}
light
{{< /alert >}}

{{< alert title="dark" color="dark">}}
dark
{{< /alert >}}


## notice
```python
{{/* Available notice types: warning, info, note, tip */}}
{{- $noticeType := .Get 0 | default "note" -}}

{{/* Workaround markdownify inconsistency for single/multiple paragraphs */}}
{{- $raw := (markdownify .Inner | chomp) -}}
{{- $block := findRE "(?is)^<(?:address|article|aside|blockquote|canvas|dd|div|dl|dt|fieldset|figcaption|figure|footer|form|h(?:1|2|3|4|5|6)|header|hgroup|hr|li|main|nav|noscript|ol|output|p|pre|section|table|tfoot|ul|video)\\b" $raw 1 -}}

{{/* Load the css if it's the first time */}}
{{- if not ($.Page.Scratch.Get "notice-style-loaded-flag") -}}
<style type="text/css">
    /* Light theme */
    .notice {
        --title-color: #fff;
        --title-background-color: #6be;
        --content-color: #444;
        --content-background-color: #e7f2fa;
    }

    .notice.info {
        --title-background-color: #fb7;
        --content-background-color: #fec;
    }

    .notice.tip {
        --title-background-color: #5a5;
        --content-background-color: #efe;
    }

    .notice.warning {
        --title-background-color: #c33;
        --content-background-color: #fee;
    }

    /* Dark theme */
    @media (prefers-color-scheme:dark) {
        .notice {
            --title-color: #fff;
            --title-background-color: #069;
            --content-color: #ddd;
            --content-background-color: #023;
        }

        .notice.info {
            --title-background-color: #a50;
            --content-background-color: #420;
        }

        .notice.tip {
            --title-background-color: #363;
            --content-background-color: #121;
        }

        .notice.warning {
            --title-background-color: #800;
            --content-background-color: #400;
        }
    }

    body.dark .notice {
        --title-color: #fff;
        --title-background-color: #069;
        --content-color: #ddd;
        --content-background-color: #023;
    }

    body.dark .notice.info {
        --title-background-color: #a50;
        --content-background-color: #420;
    }

    body.dark .notice.tip {
        --title-background-color: #363;
        --content-background-color: #121;
    }

    body.dark .notice.warning {
        --title-background-color: #800;
        --content-background-color: #400;
    }

    /* Content */
    .notice {
        padding: 18px;
        line-height: 24px;
        margin-bottom: 24px;
        border-radius: 4px;
        color: var(--content-color);
        background: var(--content-background-color);
    }

    .notice p:last-child {
        margin-bottom: 0
    }

    /* Title */
    .notice-title {
        margin: -18px -18px 12px;
        padding: 4px 18px;
        border-radius: 4px 4px 0 0;
        font-weight: 700;
        color: var(--title-color);
        background: var(--title-background-color);
    }

    /* Icon */
    .icon-notice {
        display: inline-flex;
        align-self: center;
        margin-right: 8px;
    }

    .icon-notice img,
    .icon-notice svg {
        height: 1em;
        width: 1em;
        fill: currentColor;
    }

    .icon-notice img,
    .icon-notice.baseline svg {
        top: .125em;
        position: relative;
    }
</style>
{{- $.Page.Scratch.Set "notice-style-loaded-flag" true -}}
{{- end -}}

<div class="notice {{ $noticeType }}" {{ if len .Params | eq 2 }} id="{{ .Get 1 }}" {{ end }}>
    <p class="notice-title">
        <span class="icon-notice baseline">
            {{ printf "icons/%s.svg" $noticeType | readFile | safeHTML }}
        </span>
        {{- if eq $noticeType "warning" -}}
        警告
        {{end}}
        {{- if eq $noticeType "note" -}}
        注意
        {{end}}
        {{- if eq $noticeType "info" -}}
        信息
        {{end}}
        {{- if eq $noticeType "tip" -}}
        提示
        {{end}}
    </p>
    {{- if or $block (not $raw) }}{{ $raw }}{{ else }}<p>{{ $raw }}</p>{{ end -}}
</div>
```






