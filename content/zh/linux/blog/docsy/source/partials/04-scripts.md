---
title: scripts.html
description: scripts.html
date: 2023-10-30
weight: 4000
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


## scripts.html


```python
{{ $needKaTeX  := or .Site.Params.katex.enable .Params.math .Params.chem -}}
{{ $needmhchem := or .Site.Params.katex.mhchem.enable .Params.chem -}}
{{ if ge hugo.Version "0.93.0" -}}
  {{ $needKaTeX = or $needKaTeX (.Page.Store.Get "hasKaTeX") (.Page.Store.Get "hasmhchem") -}}
  {{ $needmhchem = or $needmhchem (.Page.Store.Get "hasmhchem") -}}
{{ else -}}
  {{ if or $needKaTeX $needmhchem -}}
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
<script src="https://cdn.jsdelivr.net/npm/markmap-autoloader"></script>
{{ end -}}

{{ if .Site.Params.plantuml.enable -}}
  <script src='{{ "js/deflate.js" | relURL }}'></script>
{{ end -}}


{{ if .Params.viz }}
  <script type="text/javascript" src='{{ "js/viz-1.8.2.js" | relURL }}'> </script>
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


{{ if  $needKaTeX -}}
{{/* load stylesheet and scripts for KaTeX support */ -}}
<link rel="stylesheet" href='{{ "css/katex-0.16.10.min.css" | relURL }}'>
  {{/* The loading of KaTeX is deferred to speed up page rendering */ -}}
<script defer src='{{ "js/katex-0.16.10.min.js" | relURL }}'>
</script>
  {{ if $needmhchem -}}
  {{/* To add support for displaying chemical equations and physical units, load the mhchem extension: */ -}}
<script defer src='{{ "js/mhchem-0.16.10.min.js" | relURL }}'></script>
  {{ end -}}
  {{/* To automatically render math in text elements, include the auto-render extension: */ -}}
<script defer src='{{ "js/auto-render-0.16.10.min.js" | relURL }}'
       {{ printf "onload='renderMathInElement(%s, %s);'" (( $.Page.Site.Params.katex.html_dom_element | default "document.body" ) | safeJS ) ( printf "%s" ( $.Page.Site.Params.katex.options | jsonify )) | safeHTMLAttr }}>
</script>
{{ end -}}

{{ $jsBs := resources.Get "vendor/bootstrap/dist/js/bootstrap.bundle.js" -}}
{{ $jsBase := resources.Get "js/base.js" -}}
{{ $jsSearch := resources.Get "js/search.js" | resources.ExecuteAsTemplate "js/search.js" .Site.Home -}}
{{ $jsMarkmap := resources.Get "js/markmap.js" | resources.ExecuteAsTemplate "js/markmap.js" . -}}
{{ $jsPlantuml := resources.Get "js/plantuml.js" | resources.ExecuteAsTemplate "js/plantuml.js" . -}}
{{ $jsDrawio := resources.Get "js/drawio.js" | resources.ExecuteAsTemplate "js/drawio.js" . -}}
{{ if .Site.Params.offlineSearch -}}
  {{ $jsSearch = resources.Get "js/offline-search.js" -}}
{{ end -}}

{{ $jsArray := slice $jsBs $jsBase $jsSearch $jsPlantuml $jsMarkmap $jsDrawio -}}

{{ if .Page.Store.Get "hasmermaid" -}}
{{- partial "scripts/mermaid.html" . -}}
{{ end -}}

{{ if .Site.Params.ui.showLightDarkModeMenu -}}
  {{ $jsArray = $jsArray | append (resources.Get "js/dark-mode.js") -}}
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
<script src='{{ "js/docsearch-3.6.0.js" | relURL }}' ></script>
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

```









