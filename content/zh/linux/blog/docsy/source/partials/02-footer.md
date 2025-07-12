---
title: footer
description: footer
date: 2023-10-30
weight: 2000


viz: true
---
{{< alert >}}

- [https://gohugo.io/methods/page/](https://gohugo.io/methods/page/)

{{< /alert >}}


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


## 一、copyright.html
> layouts/partials/footer/copyright.html

```python
{{ $page := .Page -}}
{{ with .Site.Params.copyright -}}
  {{ $fromYear := "" -}}
  {{ $toYear := "" -}}
  {{ $authors := "" -}}
  {{ if reflect.IsMap . -}}
    {{ $fromYear = .from_year -}}
    {{ $toYear = .to_year -}}
    {{ $authors = .authors -}}
  {{ else -}}
    {{ $authors = . -}}
  {{ end -}}

  <span class="td-footer__copyright">&copy;
    {{ with $fromYear -}}
      {{ . }}&ndash;
    {{- end -}}
    {{ $toYear | default now.Year }}
    <span class="td-footer__authors">
      {{- $authors
            | default (printf "%s Authors" ($.Site.Title | default "Site"))
            | $page.RenderString -}}
    </span>
    {{- /* Trim WS */ -}}
  </span>

  {{- with T "footer_all_rights_reserved" -}}
    <span class="td-footer__all_rights_reserved">
      {{- . -}}
    </span>
  {{- end }}

{{- else -}}

  {{ with .Site.Copyright -}}
    {{/* For historical reasons we process this as HTML rather than markdown. */ -}}
    <span class="td-footer__copyright">
      {{- . | safeHTML -}}
    </span>
  {{- end -}}

{{ end -}}

```
- `$page := .Page`是为了保存当前页面对象，因为下面使用了`with .Site.Params.copyright` 修改了当前上下文，需要在里面的上下文中使用页面的方法`$page.RenderString`
- `.Site.Params.copyright` 获取配置文件hugo.yaml中的配置项Params.copyright，Params.links是一个map
  - fromYear
  - toYear
  - authors
    - copyright中定义优先
    - 其次为网站标题`$.Site.Title`
    - 最后为Site
- reflect.IsMap 判断是否为键值对对象
- reflect.IsSlice 判断是否为列表
- `$toYear | default now.Year` 没有定义toYear，则用当前年份
- `$page.RenderString` 将markdown渲染为HTML
- `&ndash;`表示横杠
- `.Site.Copyright` 以前的配置项，现在已经改到了Params中
- `safeHTML` 表示渲染为HTML


文件内容：hugo.yaml
```yaml
params:
  copyright:
    authors: igoodful All Rights Reserved. [\[ 鄂ICP备2023010493号-1 \]](https://beian.miit.gov.cn/)
    from_year: 2020
```



## 二、links.html
> layouts/partials/footer/links.html

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

        "left.html" -> "links.html";
        "right.html" -> "links.html";

}
```


```python
<ul class="td-footer__links-list">
  {{ range . }}
  <li class="td-footer__links-item" data-bs-toggle="tooltip" title="{{ .name }}" aria-label="{{ .name }}">
    <a target="_blank" rel="noopener" href="{{ .url }}" aria-label="{{ .name }}">
      <i class="{{ .icon }}"></i>
    </a>
  </li>
  {{ end }}
</ul>

```
- 此处的点代表一个键值列表
- 键值的属性有：
  - name
  - url
  - icon
  - desc


文件内容：hugo.yaml
```yaml
params:
  links:
    user:
      - name: User mailing list
        url: https://mail.google.com/mail
        icon: fa fa-envelope
        desc: Discussion and help from your fellow users
      - name: Twitter
        url: https://twitter.com/igoodful
        icon: fab fa-twitter
        desc: Follow us on Twitter to get the latest news!
      - name: Stack Overflow
        url: https://github.com/igoodful
        icon: fab fa-stack-overflow
        desc: Practical questions and curated answers
    developer:
      - name: GitHub
        url: https://github.com/google/docsy
        icon: fab fa-github
        desc: Development takes place here!
      - name: Slack
        url: https://igoodful.com/igoodful
        icon: fab fa-slack
        desc: Chat with other project developers
      - name: Developer mailing list
        url: https://mail.google.com/mail
        icon: fa fa-envelope
        desc: Discuss development issues around the project

```




## 三、left.html
> layouts/partials/footer/left.html

```python
{{ with .Site.Params.links -}}
  {{ with index . "user" -}}
    {{- partial "footer/links.html"  . -}}
  {{ end -}}
{{ end -}}

```
- `.Site.Params.links`  获取配置文件hugo.yaml中的配置项Params.links，Params.links是一个map
- `index`  获取map的user项




## 四、right.html
> layouts/partials/footer/right.html

```python
{{ with .Site.Params.links -}}
  {{ with index . "developer" -}}
    {{- partial "footer/links.html"  . -}}
  {{ end -}}
{{ end -}}

```

- `.Site.Params.links`  获取配置文件hugo.yaml中的配置项Params.links，Params.links是一个map
- `index`  获取map的developer项


## 五、enter.html
> layouts/partials/footer/center.html
> 共两行：一行版权与隐私政策；一行关于URL

```python
{{ partial "footer/copyright.html"  . -}}

{{ with .Site.Params.privacy_policy -}}
  <span class="ms-2"><a href="{{ . }}" target="_blank" rel="noopener">{{ T "footer_privacy_policy" }}</a></span>
{{- end -}}

{{ if ne .Site.Params.ui.footer_about_disable nil -}}
  {{ warnf "Config parameter '.params.ui.footer_about_disable' is DEPRECATED, use '.params.ui.footer_about_enable' instead." -}}
{{ end -}}

{{ if or .Site.Params.ui.footer_about_enable (eq .Site.Params.ui.footer_about_disable false) -}}
  {{ with .Site.GetPage "about" -}}
    <p class="td-footer__about mt-2"><a href="{{ .RelPermalink }}">{{ .Title }}</a></p>
  {{- end -}}
{{ end -}}

```

- `.Site.Params.privacy_policy` 隐私政策：获取配置文件hugo.yaml中的配置项Params.privacy_policy，Params.privacy_policy是一个网址的url
- `ne .Site.Params.ui.footer_about_disable nil` 表示：`.Site.Params.ui.footer_about_disable`与`nil`不相等时为真，则会给出警告，因此需要禁用这个玩意
  - 在执行hugo server时会给出警告内容：`WARN  Config parameter '.params.ui.footer_about_disable' is DEPRECATED, use '.params.ui.footer_about_enable' instead.`
- `eq .Site.Params.ui.footer_about_disable false` 表示：`.Site.Params.ui.footer_about_disable`与`false`相等时为真
- `or .Site.Params.ui.footer_about_enable (eq .Site.Params.ui.footer_about_disable false)`，or表示：返回第一个不为空的参数或最后一个参数。可以有任意多个参数
- `.Site.GetPage` 表示获取某个列表页或者单页，名称需要全局唯一，比如test/docsy/install/，about，/about，只写后面多个路径不可用，只写文件名称需要保证唯一，写全路径需要从前往后写
  - 返回的是页面对象，比如.Site.GetPage "about" 则返回：Page(/about)对象，页面对象Page(/about)的`.RelPermalink`属性为该页面的相对路径
  - 一定要唯一，否则报错：`<.Site.GetPage>: error calling GetPage: page reference "/layouts" is ambiguous`
  - 报错为：`ERROR Rebuild failed: render: failed to render pages: render of "section" failed: "/Users/igoodful/hugo/maths/layouts/test/list.html:43:15": execute of template failed at <.Site.GetPage>: error calling GetPage: page reference "/page" is ambiguous`
- `.RelPermalink` 表示页面的相对路径，也就是ip和端口后面的路径
- `.RelPermalink` 表示页面的绝对路径，也就是ip和端口整个路径
- `.Title` 表示页面的标题，


文件内容：hugo.yaml
```yaml
params:
  privacy_policy: https://www.igoodful.com/

```


