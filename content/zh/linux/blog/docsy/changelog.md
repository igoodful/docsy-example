---
title: 变更记录
description: changelog
date: 2023-11-17
weight: 30000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}

## 20240323 graphviz

{{<alert>}}

- 添加支持`graphviz`
  - [graphviz在线](https://viz-js.com/)
  - [viz.js 1.8.2](https://cdn.bootcss.com/viz.js/1.8.2/viz.js)
  - [viz.js 1.8.2 github](https://github.com/mdaines/viz-js/releases/download/v1.8.2/viz.js)
  - [viz.js github](https://github.com/mdaines/viz-js/releases)

{{</alert>}}


#### scripts.html
`C:\hugo\now\maths\layouts\partials\scripts.html`
```sql
{{ if .Params.viz }}
  <script type="text/javascript" src="https://cdn.bootcss.com/viz.js/1.8.2/viz.js"> </script>
  <script type="text/javascript">
  (function(){
    var vizPrefix = "language-viz-";
    Array.prototype.forEach.call(document.querySelectorAll("[class^=" + vizPrefix + "]"), function(x){
      var engine;
      x.getAttribute("class").split(" ").forEach(function(cls){
        if (cls.startsWith(vizPrefix)) {
          engine = cls.substr(vizPrefix.length);
        }
      });
      var image = new DOMParser().parseFromString(Viz(x.innerText, {format:"svg", engine:engine}), "image/svg+xml");
      x.parentNode.insertBefore(image.documentElement, x);
      x.style.display = 'none'
      x.parentNode.style.backgroundColor = "white"
    });
  })();
  </script>
{{ end }}
```
#### 本地化
将`viz.js`本地化：
- 下载`https://github.com/mdaines/viz-js/releases/download/v1.8.2/viz.js`
- 放置：`C:\hugo\now\maths\static\js/viz.js`


最终的`C:\hugo\now\maths\layouts\partials\scripts.html`
```sql
{{ if .Params.viz }}
  <script type="text/javascript" src="/js/viz.js"> </script>
  <script type="text/javascript">
  (function(){
    var vizPrefix = "language-viz-";
    Array.prototype.forEach.call(document.querySelectorAll("[class^=" + vizPrefix + "]"), function(x){
      var engine;
      x.getAttribute("class").split(" ").forEach(function(cls){
        if (cls.startsWith(vizPrefix)) {
          engine = cls.substr(vizPrefix.length);
        }
      });
      var image = new DOMParser().parseFromString(Viz(x.innerText, {format:"svg", engine:engine}), "image/svg+xml");
      x.parentNode.insertBefore(image.documentElement, x);
      x.style.display = 'none'
      x.parentNode.style.backgroundColor = "white"
    });
  })();
  </script>
{{ end }}
```

#### markdown文件上需新增
```yaml
viz: true
```

#### 完整的`scripts.html`
> `C:\hugo\now\maths\layouts\partials\scripts.html`
```sql
{{ $needKaTeX  := or .Site.Params.katex.enable .Params.math .Params.chem -}}
{{ $needmhchem := or .Site.Params.katex.mhchem.enable .Params.chem -}}
{{ $needmermaid := .Site.Params.mermaid.enable -}}
{{ if ge hugo.Version "0.93.0" -}}
    {{ with .Site.Params.mermaid }}
        {{ $needmermaid = true }}
    {{ end }}
  {{ $needKaTeX = or $needKaTeX (.Page.Store.Get "hasKaTeX") (.Page.Store.Get "hasmhchem") -}}
  {{ $needmhchem = or $needmhchem (.Page.Store.Get "hasmhchem") -}}
  {{ $needmermaid = or $needmermaid (.Page.Store.Get "hasmermaid") -}}
{{ else -}}
  {{ if or $needKaTeX $needmhchem $needmermaid -}}
    {{ warnf "Outdated Hugo version %s, consider upgrading to make full use of all theme features" hugo.Version  }}
  {{ end -}}
{{ end -}}

{{ if .Site.Params.markmap.enable -}}
<style>
.markmap > svg {
  width: 100%;
  height: 300px;
}
</style>
<script>
window.markmap = {
  autoLoader: {
      manual: true,
      onReady() {
        const { autoLoader, builtInPlugins } = window.markmap;
        autoLoader.transformPlugins = builtInPlugins.filter(plugin => plugin.name !== 'prism');
      },
  },
};
</script>
<script src="{{ .Site.BaseURL }}js/markmap-autoloader"></script>
{{ end -}}

{{ if .Site.Params.plantuml.enable -}}
  <script src='{{ "js/deflate.js" | relURL }}'></script>
{{ end -}}

{{ if  $needKaTeX -}}
{{/* load stylesheet and scripts for KaTeX support */ -}}
<link rel="stylesheet" href="{{ .Site.BaseURL }}css/katex.min.css"
      integrity="sha512-mQwom8Ns4op+H29oDkD/LXO/OsXPvCFfkgZkFAVrhhePzRLU8NUI3Nkm43NhWUSmj3p5Cca2HTEkMQmXQRwDQQ==" crossorigin="anonymous">
  {{/* The loading of KaTeX is deferred to speed up page rendering */ -}}
<script defer src="{{ .Site.BaseURL }}js/katex.min.js"
        integrity="sha512-sHSNLECRJSK+BFs7E8DiFc6pf6n90bLI85Emiw1jhreduZhK3VXgq9l4kAt9eTIHYAcuQBKHL01QKs4/3Xgl8g=="
        crossorigin="anonymous">
</script>
  {{ if $needmhchem -}}
  {{/* To add support for displaying chemical equations and physical units, load the mhchem extension: */ -}}
<script defer src="{{ .Site.BaseURL }}js/mhchem.min.js"
        integrity="sha512-V1hl0fnOXW6Cdqe5ZVqtw8TBpJVpu3XRDRQti96j/04+tMarPrCdXEUE3UdfvfKYTpOn9DV4zEZBVr0HhDiuiQ=="
        crossorigin="anonymous">
</script>
  {{ end -}}
  {{/* To automatically render math in text elements, include the auto-render extension: */ -}}
<script defer src="{{ .Site.BaseURL }}js/auto-render.min.js"
       integrity="sha512-iWiuBS5nt6r60fCz26Nd0Zqe0nbk1ZTIQbl3Kv7kYsX+yKMUFHzjaH2+AnM6vp2Xs+gNmaBAVWJjSmuPw76Efg==" crossorigin="anonymous"
       {{ printf "onload='renderMathInElement(%s, %s);'" (( $.Page.Site.Params.katex.html_dom_element | default "document.body" ) | safeJS ) ( printf "%s" ( $.Page.Site.Params.katex.options | jsonify )) | safeHTMLAttr }}>
</script>
{{ end -}}
<script src="{{ .Site.BaseURL }}js/bootstrap.bundle.min.js"  integrity="sha384-Fy6S3B9q64WdZWQUiU+q4/2Lc9npb8tCaSX9FK7E8HnRr0Jz8D6OP9dO5Vg3Q9ct" crossorigin="anonymous"></script>

{{ $jsBase := resources.Get "js/base.js" -}}
{{ $jsAnchor := resources.Get "js/anchor.js" -}}
{{ $jsSearch := resources.Get "js/search.js" | resources.ExecuteAsTemplate "js/search.js" .Site.Home -}}
{{ $jsMermaid := resources.Get "js/mermaid.js" | resources.ExecuteAsTemplate "js/mermaid.js" . -}}
{{ $jsMarkmap := resources.Get "js/markmap.js" | resources.ExecuteAsTemplate "js/markmap.js" . -}}
{{ $jsPlantuml := resources.Get "js/plantuml.js" | resources.ExecuteAsTemplate "js/plantuml.js" . -}}
{{ $jsDrawio := resources.Get "js/drawio.js" | resources.ExecuteAsTemplate "js/drawio.js" . -}}
{{ if .Site.Params.offlineSearch -}}
  {{ $jsSearch = resources.Get "js/offline-search.js" -}}
{{ end -}}

{{ $jsArray := slice  $jsBase $jsAnchor $jsSearch $jsPlantuml $jsMarkmap $jsDrawio -}}

{{ if $needmermaid -}}
{{ $jsArray = $jsArray | append $jsMermaid -}}
<script src="/js/mermaid.min.js"  crossorigin="anonymous"></script>
{{ end -}}

{{ $js := $jsArray | resources.Concat "js/main.js" -}}
{{ if hugo.IsProduction -}}
  {{ $js := $js | minify | fingerprint -}}
  <script src="{{ $js.RelPermalink }}" integrity="{{ $js.Data.Integrity }}" crossorigin="anonymous"></script>
{{ else -}}
  <script src="{{ $js.RelPermalink }}"></script>
{{ end -}}

{{ if .Site.Params.prism_syntax_highlighting -}}
  <script src='{{ "js/prism.js" | relURL }}'></script>
{{ else if ( not .Site.Params.disable_click2copy_chroma ) -}}
  {{ $c2cJS := resources.Get "js/click-to-copy.js" -}}
  {{ if hugo.IsProduction -}}
    {{ $c2cJS = $c2cJS | minify | fingerprint -}}
  {{ end -}}
  <script defer src="{{ $c2cJS.RelPermalink }}" {{ with $c2cJS.Data.Integrity -}}
    integrity="{{ . }}" {{ end -}}
    crossorigin="anonymous"></script>
{{ end -}}

{{ if and .Site.Params.search (isset .Site.Params.search "algolia") -}}
  {{ template "algolia/scripts" .Site.Params.search.algolia -}}
{{ end -}}
<script src='{{ "js/tabpane-persist.js" | relURL }}'></script>
{{ partial "hooks/body-end.html" . -}}

{{ define "algolia/scripts" -}}
<script src="{{ .Site.BaseURL }}js/js@3"
  integrity="sha512-sQFwlNnBlEyWdI4EwJ9d2wjViu0ZPmIMjPP8kCmBoOjqcljGndf2gb4mldT4ZHxKCQcrdJ0+rxnMFKHuGWB7ag=="
  crossorigin="anonymous" ></script>
<script type="text/javascript">
const containers = ['#docsearch-0', '#docsearch-1'];
for (let c of containers) {
  docsearch({
    container: c,
    appId: {{ .appId | default "R2IYF7ETH7" }},
    apiKey: {{ .apiKey | default "599cec31baffa4868cae4e79f180729b" }},
    indexName: {{ .indexName | default "docsearch" }},
  });
}
</script>
{{ end -}}

{{ if .Params.viz }}
  <script type="text/javascript" src="/js/viz.js"> </script>
  <script type="text/javascript">
  (function(){
    var vizPrefix = "language-viz-";
    Array.prototype.forEach.call(document.querySelectorAll("[class^=" + vizPrefix + "]"), function(x){
      var engine;
      x.getAttribute("class").split(" ").forEach(function(cls){
        if (cls.startsWith(vizPrefix)) {
          engine = cls.substr(vizPrefix.length);
        }
      });
      var image = new DOMParser().parseFromString(Viz(x.innerText, {format:"svg", engine:engine}), "image/svg+xml");
      x.parentNode.insertBefore(image.documentElement, x);
      x.style.display = 'none'
      x.parentNode.style.backgroundColor = "white"
    });
  })();
  </script>
{{ end }}

```


## 20240324 MathJax

#### scripts.html
`C:\hugo\now\maths\layouts\partials\scripts.html`


```sql
{{ if  .Params.math   }}
  <script type="text/javascript">
    window.MathJax = {
      tex2jax: {
        inlineMath: [['$','$'], ['\\(','\\)']],
        displayMath: [['$$','$$'], ['\[','\]']],
        processEscapes: true,
        processEnvironments: true,
        skip,
        TeX: { equationNumbers: { autoNumber: "AMS" },
          extensions: ["AMSmath.js", "AMSsymbols.js", "color.js"] }
      },
      AuthorInit: function () {
        MathJax.Hub.Register.StartupHook("Begin",function () {
          MathJax.Hub.Queue(function() {
            var all = MathJax.Hub.getAllJax(), i;
            for(i = 0; i < all.length; i += 1) {
              all[i].SourceElement().parentNode.className += ' has-jax';
            }
          })
        });
      }
    };
  </script>
  <script  type="text/javascript"
    src="https://cdn.bootcss.com/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>
{{ end }}

```


#### markdown文件上需新增
```yaml
math: true
```










