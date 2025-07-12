---
title: 简码
description: 简码
date: 2023-10-01
weight: 3
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 配置：hugo.toml

短代码模板有一个简单的查找顺序：
- /layouts/shortcodes/<SHORTCODE>.html
- /themes/<THEME>/layouts/shortcodes/<SHORTCODE>.html

## 举例说明
- /layouts/shortcodes/year.html




{{<note>}}
<!--

-->
如果你在 macOS 上使用 Docker Desktop 和 kind，
你可以在菜单项 **Preferences > Resources > File Sharing**
下添加 `/tmp` 作为共享目录。
{{</note>}}


{{< caution >}}
<!--

-->
这些步骤不考虑网络和存储插件等第三方扩展。
{{< /caution >}}


{{< warning >}}
<!--

-->
这些步骤不考虑网络和存储插件等第三方扩展。
{{< /warning >}}


## 举例说明

### pageinfo
```sql
{{ $_hugo_config := `{ "version": 1 }` }}
{{ $color := .Get "color" | default "primary" }}
<div class="pageinfo pageinfo-{{ $color }}">
{{ .Inner }}
</div>
```


### alert


```sql
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



<div class="alert alert-primary" role="alert">

class alert alert-primary
</div>

{{<note>}}
class alert alert-primary
{{</note>}}





### 引入kubernetes官网上的node、caution、warning短码

- [note](https://github.com/kubernetes/website/blob/main/layouts/shortcodes/note.html)
- [caution](https://github.com/kubernetes/website/blob/main/layouts/shortcodes/caution.html)
- [warning](https://github.com/kubernetes/website/blob/main/layouts/shortcodes/warning.html)


1. 添加文件project\assets\scss\_variables_project.scss，内容如下：
```scss
// kubernetes
@import "skin";
@import "custom";
$primary: #3371e3;

// 引入自定义字体
$font-family-serif: 'YourCustomFont', serif;
$font-family-sans-serif: 'YourCustomFont', sans-serif;
@font-face {
        font-family: 'YourCustomFont';
        src: url('/fonts/AirbnbCereal-Light.ttf') format('truetype');
        font-weight: normal;
        font-style: normal;
}

```

2. 添加文件project\assets\scss\_custom.scss，内容如下：
```scss

.mermaid {
  overflow-x: auto;
  max-width: 80%;
  border: 1px solid rgb(222, 226, 230);
  border-radius: 5px;
  margin-bottom: 1rem;
  padding-top: 1rem;
  padding-bottom: 1rem;

  // mermaid diagram - sequence diagram
  .actor {
    fill: #326ce5 !important;
  }
  text.actor {
    font-size: 18px !important;
    stroke: white !important;
    fill: white !important;
  }
  .activation0 {
    fill: #c9e9ec !important;
  }
}

// blockquotes and callouts
body {
  .alert {
    // Override Docsy styles
    padding: 0.4rem 0.4rem 0.4rem 1rem;
    border-top: 1px solid #eee;
    border-bottom: 1px solid #eee;
    border-right: 1px solid #eee;
    border-radius: 0.25em;
    border-left-width: 0.5em; // fallback in case calc() is missing
    background: #fff;
    color: #000;
    margin-top: 0.5em;
    margin-bottom: 0.5em;
  }
  // Set minimum width and radius for alert color
  .alert {
      border-left-width: calc(max(0.5em, 4px));
      border-top-left-radius: calc(max(0.5em, 4px));
      border-bottom-left-radius: calc(max(0.5em, 4px));
  }
  .alert.callout.caution {
    border-left-color: #f0ad4e;
  }
  .alert.callout.note {
    border-left-color: #428bca;
  }
  .alert.callout.warning {
    border-left-color: #d9534f;
  }
  .alert.third-party-content {
    border-left-color: #444;
  }

  h1:first-of-type + .alert.callout {
    margin-top: 1.5em;
  }
}

// Special color for third party content disclaimers
.alert.third-party-content { border-left-color: #222 };


```

3. 添加文件project\assets\scss\_skin.scss，内容如下
```scss
$blue: #3371e3;
$light-grey: #f7f7f7;
$dark-grey: #303030;
$medium-grey: #4c4c4c;
$white: #ffffff;
```

4. 将短码文件复制到对应目录
   - https://github.com/kubernetes/website/blob/main/layouts/shortcodes/note.html    复制到 project\layouts\shortcodes\note.html
   - https://github.com/kubernetes/website/blob/main/layouts/shortcodes/caution.html 复制到 project\layouts\shortcodes\caution.html
   - https://github.com/kubernetes/website/blob/main/layouts/shortcodes/warning.html 复制到 project\layouts\shortcodes\warning.html
   - https://github.com/kubernetes/website/blob/main/layouts/shortcodes/mermaid.html 复制到 project\layouts\shortcodes\mermaid.html


### note
https://github.com/kubernetes/website/blob/main/layouts/shortcodes/note.html 内容如下：
```html
<div class="alert alert-info note callout" role="alert">
  <strong>{{ "说明：" }}</strong> {{ trim .Inner " \n" | markdownify }}
</div>
```

{{<note>}}
<!---->
SCSS 中的函数是 SASS 功能的重要组成部分，它们允许我们定义可在整个样式表中重用的复杂操作。
{{</note>}}

### caution
https://github.com/kubernetes/website/blob/main/layouts/shortcodes/caution.html 内容如下：

```html
<div class="alert alert-warning caution callout" role="alert">
  <strong>{{ "注意：" }}</strong> {{ trim .Inner " \n" | markdownify }}
</div>
```
{{<caution>}}
<!---->
SCSS 中的函数是 SASS 功能的重要组成部分，它们允许我们定义可在整个样式表中重用的复杂操作。
{{</caution>}}


### warning

https://github.com/kubernetes/website/blob/main/layouts/shortcodes/warning.html 内容如下：

```html
<div class="alert alert-danger warning callout" role="alert">
  <strong>{{ "警告：" }}</strong> {{ trim .Inner " \n" | markdownify }}
</div>
```
{{<warning>}}
<!---->
SCSS 中的函数是 SASS 功能的重要组成部分，它们允许我们定义可在整个样式表中重用的复杂操作。
{{</warning>}}





### mermaid
https://github.com/kubernetes/website/blob/main/layouts/shortcodes/mermaid.html 内容如下：
```html
<figure>
  <div class="mermaid">{{.Inner}}</div>
</figure>
<!-- Hide content and error if JS is disabled. -->
<noscript>
  <div class="alert alert-secondary callout" role="alert">
    <em class="javascript-required"
      >{{ "javascript_required" | markdownify }}</em
    >
  </div>
</noscript>
```

示例
```sql

{{ /*<mermaid> */}}
graph BT
    subgraph "zoneB"
        p3(Pod) --> n3(Node3)
        n4(Node4)
    end
    subgraph "zoneA"
        p1(Pod) --> n1(Node1)
        p2(Pod) --> n2(Node2)
    end

classDef plain fill:#ddd,stroke:#fff,stroke-width:4px,color:#000;
classDef k8s fill:#326ce5,stroke:#fff,stroke-width:4px,color:#fff;
classDef cluster fill:#fff,stroke:#bbb,stroke-width:2px,color:#326ce5;
class n1,n2,n3,n4,p1,p2,p3 k8s;
class p4 plain;
class zoneA,zoneB cluster;
{{/*< /mermaid > */}}

```

{{<mermaid>}}
graph BT
    subgraph "zoneB"
        p3(Pod) --> n3(Node3)
        n4(Node4)
    end
    subgraph "zoneA"
        p1(Pod) --> n1(Node1)
        p2(Pod) --> n2(Node2)
    end

classDef plain fill:#ddd,stroke:#fff,stroke-width:4px,color:#000;
classDef k8s fill:#326ce5,stroke:#fff,stroke-width:4px,color:#fff;
classDef cluster fill:#fff,stroke:#bbb,stroke-width:2px,color:#326ce5;
class n1,n2,n3,n4,p1,p2,p3 k8s;
class p4 plain;
class zoneA,zoneB cluster;
{{< /mermaid >}}



## Chart.js


在Hugo网站中，你可以使用一个短代码（Shortcode）来创建一个简单的函数图像。你需要使用D3.js或Chart.js等前端库来绘制图像。下面是一个使用Chart.js的例子，它能够根据你提供的X轴和Y轴数据绘制出图像。

### 1. 添加Chart.js库

首先，你需要将Chart.js库添加到你的Hugo网站的`head`部分。可以在`layouts/partials/head.html`或`layouts/_default/baseof.html`文件中加入以下内容：

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

### 2. 创建Shortcode

在`layouts/shortcodes/`目录下创建一个新的Shortcode文件，例如`chart.html`：

```html
<canvas id="{{ .Get "id" }}" width="400" height="200"></canvas>
<script>
document.addEventListener('DOMContentLoaded', function() {
    var ctx = document.getElementById('{{ .Get "id" }}').getContext('2d');
    var chart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: [{{ .Get "xvalues" }}],
            datasets: [{
                label: '{{ .Get "label" }}',
                data: [{{ .Get "yvalues" }}],
                borderColor: 'rgba(75, 192, 192, 1)',
                borderWidth: 2,
                fill: false
            }]
        },
        options: {
            scales: {
                x: {
                    beginAtZero: true
                },
                y: {
                    beginAtZero: true
                }
            }
        }
    });
});
</script>
```

### 3. 使用Shortcode

在你的Markdown文件中，使用这个Shortcode来生成图表。你只需要提供X轴和Y轴的数组值：


`\{\{\< chart id="myChart" xvalues="'0', '1', '2', '3', '4'" yvalues="0, 1, 4, 9, 16" label="y = x^2" \>\}\}`


### 4. 渲染图像

当你在Hugo中渲染这个页面时，`Chart.js`将使用你提供的X轴和Y轴的数据来生成一个线图。

### 代码说明

- `id`: Canvas的ID，确保页面上每个图表都有唯一的ID。
- `xvalues`: X轴的数据，格式为逗号分隔的字符串。
- `yvalues`: Y轴的数据，格式为逗号分隔的字符串。
- `label`: 图表的标签，可以是任何描述性的字符串。

这个Shortcode使用了`Chart.js`库绘制线图，`Chart.js`提供了丰富的图表类型和自定义选项。如果你需要更多自定义功能，可以根据需要修改`options`部分。



