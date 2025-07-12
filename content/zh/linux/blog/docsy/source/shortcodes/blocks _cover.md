---
title: blocks/cover
description: blocks/cover
date: 2023-10-30
weight: 10000


viz: true
---
{{< alert >}}

- [https://github.com/martignoni/hugo-notice.git](https://github.com/martignoni/hugo-notice.git)
- [https://www.docsy.dev/docs/examples/](https://www.docsy.dev/docs/examples/)

{{< /alert >}}

## blocks/cover.html
> `layouts/shortcodes/blocks/cover.html`

```python
{{ $_hugo_config := `{ "version": 1 }` -}}
{{ $blockID := printf "td-cover-block-%d" .Ordinal -}}
{{ $promo_image := (.Page.Resources.ByType "image").GetMatch "**background*" -}}
{{ $logo_image := (.Page.Resources.ByType "image").GetMatch "**logo*" -}}
{{ $col_id := .Get "color" | default "dark" -}}
{{ $image_anchor := .Get "image_anchor" | default "smart" -}}
{{ $logo_anchor := .Get "logo_anchor" | default "smart" -}}
{{/* Height can be one of: auto, min, med, max, full. */ -}}
{{ $height := .Get "height" | default "max" -}}

{{ with $promo_image -}}
{{ $promo_image_big := . -}}
{{ $promo_image_small := . -}}
{{ if ne $promo_image.MediaType.SubType "svg" -}}
  {{ $promo_image_big = .Fill (printf "1920x1080 %s" $image_anchor) -}}
  {{ $promo_image_small = .Fill (printf "960x540 %s" $image_anchor) -}}
{{ end -}}
<link rel="preload" as="image" href="{{ $promo_image_small.RelPermalink }}" media="(max-width: 1200px)">
<link rel="preload" as="image" href="{{ $promo_image_big.RelPermalink }}" media="(min-width: 1200px)">
<style>
#{{ $blockID }} {
  background-image: url({{ $promo_image_small.RelPermalink }});
}
@media only screen and (min-width: 1200px) {
  #{{ $blockID }} {
    background-image: url({{ $promo_image_big.RelPermalink }});
  }
}
</style>
{{ end -}}

<section id="{{ $blockID }}" class="row td-cover-block td-cover-block--height-{{ $height -}}
  {{ if not .Site.Params.ui.navbar_translucent_over_cover_disable }} js-td-cover
  {{- end }} td-overlay td-overlay--dark -bg-{{ $col_id }}">
  <div class="col-12">
    <div class="container td-overlay__inner">
      <div class="text-center">
        {{ with .Get "title" }}<h1 class="display-1 mt-0 mt-md-5 pb-4">{{ $title := . }}{{ with $logo_image }}{{ $logo_image_resized := (.Fit (printf "70x70 %s" $logo_anchor)) }}<img class="td-cover-logo" src="{{ $logo_image_resized.RelPermalink }}" alt="{{ $title | html }} Logo">{{ end }}{{ $title | html }}</h1>{{ end }}
        {{ with .Get "subtitle" }}<p class="display-2 text-uppercase mb-0">{{ . | html }}</p>{{ end }}
        <div class="pt-3 lead">
          {{ if eq .Page.File.Ext "md" }}
              {{ .Inner | markdownify }}
          {{ else }}
              {{ .Inner | htmlUnescape | safeHTML }}
          {{ end }}
        </div>
      </div>
    </div>
  </div>
  {{ with .Get "byline" | default "" -}}
    <div class="byline">{{ . }}</div>
  {{- end }}
</section>
{{/**/ -}}
```




这个Hugo短代码创建了一个封面区块（cover block），其作用是展示一个背景图片、标题、子标题和内容。这段代码使用了Hugo的模板语言和一些嵌入式Go模板函数。下面是每一行代码的详解：

```python
{{ $_hugo_config := `{ "version": 1 }` -}}

```
设置Hugo配置的版本号为1。这是为了确保模板与特定版本的Hugo配置兼容。

```python
{{ $blockID := printf "td-cover-block-%d" .Ordinal -}}
```

```python
{{ $blockID := printf "%d" .Ordinal -}}
<div>

{{$blockID}}

</div>
```

生成一个唯一的块ID，使用当前块的序号（.Ordinal）来确保ID的唯一性。

```python
{{ $promo_image := (.Page.Resources.ByType "image").GetMatch "**background*" -}}
{{ $logo_image := (.Page.Resources.ByType "image").GetMatch "**logo*" -}}
```

查找页面资源中类型为“image”的图片文件，分别获取匹配`"*background"`和`*logo`的图片。`$promo_image`用于背景图片，`$logo_image`用于logo图片。
```python
{{ $col_id := .Get "color" | default "dark" -}}
```
获取短代码参数中的颜色（color），如果未提供则默认使用“dark”。

```python
{{ $image_anchor := .Get "image_anchor" | default "smart" -}}
{{ $logo_anchor := .Get "logo_anchor" | default "smart" -}}
```
获取短代码参数中的图片锚点（image_anchor）和logo锚点（logo_anchor），如果未提供则默认使用“smart”。

```python
{{ $height := .Get "height" | default "max" -}}
```
获取短代码参数中的高度（height），如果未提供则默认使用“max”。

```python
{{ with $promo_image -}}
```
如果存在背景图片，则执行下面的代码块。

```python
{{ $promo_image_big := . -}}
{{ $promo_image_small := . -}}
```
初始化大图片和小图片变量，默认都为背景图片。

```python
{{ if ne $promo_image.MediaType.SubType "svg" -}}
  {{ $promo_image_big = .Fill (printf "1920x1080 %s" $image_anchor) -}}
  {{ $promo_image_small = .Fill (printf "960x540 %s" $image_anchor) -}}
{{ end -}}
```
如果背景图片的类型不是SVG，则根据锚点（$image_anchor）调整图片大小。大图片调整为1920x1080，小图片调整为960x540。


<link rel="preload" as="image" href="{{ $promo_image_small.RelPermalink }}" media="(max-width: 1200px)">
<link rel="preload" as="image" href="{{ $promo_image_big.RelPermalink }}" media="(min-width: 1200px)">
预加载背景图片，以便在不同屏幕尺寸下快速显示。

```python
<style>
#{{ $blockID }} {
  background-image: url({{ $promo_image_small.RelPermalink }});
}
@media only screen and (min-width: 1200px) {
  #{{ $blockID }} {
    background-image: url({{ $promo_image_big.RelPermalink }});
  }
}
</style>
```

生成CSS样式，根据屏幕尺寸设置不同的背景图片。


{{ end -}}
结束背景图片代码块。

```python
<section id="{{ $blockID }}" class="row td-cover-block td-cover-block--height-{{ $height -}}
  {{ if not .Site.Params.ui.navbar_translucent_over_cover_disable }} js-td-cover
  {{- end }} td-overlay td-overlay--dark -bg-{{ $col_id }}">
```
生成section标签，设置ID和CSS类。根据条件添加js-td-cover类。td-overlay和td-overlay--dark用于设置覆盖层样式，-bg-{{ $col_id }}用于设置背景颜色。
```python
  <div class="col-12">
    <div class="container td-overlay__inner">
      <div class="text-center">
```
创建一个12列宽度的容器，并在其内部添加一个居中的容器。
```python
        {{ with .Get "title" }}<h1 class="display-1 mt-0 mt-md-5 pb-4">{{ $title := . }}{{ with $logo_image }}{{ $logo_image_resized := (.Fit (printf "70x70 %s" $logo_anchor)) }}<img class="td-cover-logo" src="{{ $logo_image_resized.RelPermalink }}" alt="{{ $title | html }} Logo">{{ end }}{{ $title | html }}</h1>{{ end }}
````
如果存在标题，生成一个h1标签。并且如果存在logo图片，调整大小为70x70，并插入到标题前。


        {{ with .Get "subtitle" }}<p class="display-2 text-uppercase mb-0">{{ . | html }}</p>{{ end }}
如果存在子标题，生成一个p标签。

```python
        <div class="pt-3 lead">
          {{ if eq .Page.File.Ext "md" }}
              {{ .Inner | markdownify }}
          {{ else }}
              {{ .Inner | htmlUnescape | safeHTML }}
          {{ end }}
        </div>
````
根据页面文件的扩展名，渲染内部内容。如果是Markdown文件，使用markdownify处理，否则使用htmlUnescape和safeHTML处理。


```python
  {{ with .Get "byline" | default "" -}}
    <div class="byline">{{ . }}</div>
  {{- end }}
```
如果存在byline（署名），生成一个包含byline的div标签。


</section>
结束section标签。












## 使用

#### 个人
```python
{{</* blocks/cover title="Welcome to igoodful!" image_anchor="top" height="full" */>}}

<a class="btn btn-lg btn-primary me-3 mb-4 rounded" href="/about/">
  了解更多
</a>
<a class="btn btn-lg btn-danger me-3 mb-4 rounded"  href="/maths/">
  开始学习
</a>

{{</* blocks/link-down color="info" */>}}
{{</* /blocks/cover */>}}
```



#### docsy

```python
{{</* blocks/cover title="Welcome!" image_anchor="center" height="full" color="primary" */>}}
<div class="mx-auto">
	<a class="btn btn-lg btn-primary me-3 mb-4" href="{{</* relref "/docs" */>}}">
		Learn More <i class="fa-solid fa-circle-right ms-2"></i>
	</a>
	<a class="btn btn-lg btn-secondary me-3 mb-4" href="https://example.org">
		Download <i class="fa-brands fa-github ms-2"></i>
	</a>
	<p class="lead mt-5">This program is now available in <a href="#">AppStore!</a></p>
	<div class="mx-auto mt-5">
		{{</* blocks/link-down color="info" */>}}
	</div>
</div>
{{</* /blocks/cover */>}}
```





`\{\{\< progress value="10;20;10;20;10" \>\}\}`



{{< progress value="30;20;10;20;20" >}}

===================
{{< progress value="30" >}}

===================

{{< progress value="30" >}}

===================

{{< progress value="0;60" >}}


==============










