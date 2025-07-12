---
title: 配置详解
date: 2023-10-01
weight: 1
description: 人靠衣装，美靠靓装.
icon: fa-solid fa-greater-than
---


## 右上角文档标题导航配置

```toml
# 右上角按照该文档的标题分级显示导航栏，作用：快速导航到指定位置，文档目录对于长篇文章或技术文档非常有用，可以帮助读者快速导航和定位到感兴趣的部分
[markup.tableOfContents]
# 右上角显示的结束标题号，默认为3，表示最多只能显示到###，而不会显示####，#####，######
endLevel = 6
# 右上角显示的标题号是否呈现出目录的样式，默认false，不同级别的标题之间会有较小的缩进。若设置为true，则不同级别的标题之间的层次会较深，即缩进大，
ordered = false
# 右上角显示的开始标题号，默认为2，表示一级标题不会显示，而是从二级标题才开始显示
startLevel = 1
```



## 代码高亮的配置

```toml

  [markup.highlight]
    # 是否在行号前显示锚点，默认为 false。这个选项控制是否为代码块中的行号创建锚链接。
    # 如果设置为 true，则每行代码前面都会生成一个带有行号的链接，用户可以点击链接跳转到相应的行。如果设置为 false，则不会创建这些行号链接。
    # 但是只有当lineNos为true时才生效，建议：当你喜欢简洁，那么nchorLineNos = false，lineNos = false；当你需要显示行号，则两者均为true最佳
    anchorLineNos = false
    # 是否显示行号，默认为 false，这个选项控制是否在代码块中显示行号。如果设置为 true，则会在代码块左侧显示行号。如果设置为 false，则不会显示行号
    lineNos = false

    # 是否启用代码块，默认为 true。如果设置为 true，Hugo 将支持使用三个反引号（````）包围的代码块语法。
    # 这是一种常见的 Markdown 语法，用于表示代码块。如果设置为 false，则不支持这种语法。
    # 必须设置为true
    codeFences = true

    # 是否尝试猜测代码语法，默认为 false。这个选项控制 Hugo 是否尝试自动猜测代码块中的语法高亮。
    # 如果设置为 true，Hugo 将尝试根据代码块内容猜测语法高亮。如果设置为 false，则不会自动猜测语法高亮，需要显式指定语法。
    guessSyntax = false

    # 需要高亮的行号列表，默认为空，这是一个字符串值，用于指定在代码块中要高亮显示的特定行。可以在其中列出要高亮显示的行号，用逗号分隔，例如 "1,3,5"。
    hl_Lines = ''

    # 是否启用内联代码高亮，默认为 false，如果设置为 true，Hugo 将支持内联代码高亮。这意味着您可以在文本中使用反引号来标记内联代码，并且该内联代码将以高亮显示的形式呈现。
    # 不要设置成true，会导致代码全部显示在一行中
    hl_inline = false

    # 行锚点的附加选项，默认为空，这个选项用于指定是否为每行代码创建锚链接。如果设置为 true，
    # 则每行代码前面都会生成一个带有行号的链接。与 anchorLineNos 不同，这个选项会在每行代码前都创建链接，而不仅仅是行号。
    lineAnchors = ''

    # 行号开始的数字，默认为 1，这个选项用于指定行号计数的起始值。默认情况下，行号从1开始计数，但您可以根据需要将其设置为其他值
    lineNoStart = 1


    # 是否在表格中显示行号，默认为 true
    lineNumbersInTable = true

    # 是否不使用CSS类，默认为 true，如果设置为 true，Hugo 将不会为代码块添加 CSS 类。
    # 通常，代码块会使用特定的 CSS 类来进行样式设置。如果将此选项设置为 true，则不会为代码块添加这些类。
    # 当 noClasses 为 true 时：生成的 HTML 代码中不会包含任何额外的 CSS 类。这意味着代码块的渲染将完全依赖于您的网站主题或全局 CSS 样式，
    # Hugo 不会为代码块添加任何额外的 CSS 类。这种设置通常用于那些已经具有适当代码块样式的主题中，或者如果您希望代码块不受 Hugo 自动生成的 CSS 类的影响。
    # 当 noClasses 为 false 时：生成的 HTML 代码中会包含一些额外的 CSS 类，这些类用于控制代码块的样式。这些类可以帮助您在没有额外样式的情况下美化代码块。
    # 通常，这些类是由 Hugo 自动生成的，可以通过自定义 CSS 来进一步修改代码块的样式。这种设置通常用于那些不提供特定代码块样式的主题中，或者如果您希望代码块具有一致的默认样式。
    # 选择将 noClasses 设置为 true 还是 false 取决于您的网站需求和主题设计。如果您的主题已经具有适当的代码块样式或您计划自行管理代码块样式，
    # 那么将其设置为 true 可能更合适。如果您希望使用 Hugo 提供的默认代码块样式或者需要一些额外的样式帮助，那么将其设置为 false 可能更合适。
    noClasses = true

    # 是否禁用高亮，默认为 false，肯定不要禁用，如果设置为 true，Hugo 将禁用代码高亮功能，不管是否使用了代码块语法或代码高亮语法。no highlight的含义
    noHl = false

    # 代码块的样式，默认为 tango，我自己觉得默认的挺好的，https://xyproto.github.io/splash/docs/all.html上选择自己喜欢的样式
    style = 'tango'

    # 制表符宽度，默认为 4，但我屏幕有32寸和27寸，而且我更喜欢代码有层次感，我喜欢设置成8个字符缩进
    tabWidth = 8

```


#### sidebar_menu_foldable

> 左侧菜单栏是否有箭头表示
>
> 换菜单中父级部分旁边的箭头图标来展开和折叠菜单部分

```yaml
sidebar_menu_foldable: true
```


#### breadcrumb_disable taxonomy_breadcrumb_disable
> 建议禁用这个面包屑导航链接，在网页的顶部，类似目录层次这种:
>
> 比如：/docs/mysql/title
>
> 比如：MySQL/work/backup/title


禁用：
```yaml
breadcrumb_disable: true
taxonomy_breadcrumb_disable: true

```

开启：
```yaml
breadcrumb_disable: false
taxonomy_breadcrumb_disable: false

```

#### print
> 是否显示文档目录，建议启用


启用打印时显示文档目录
```yaml
params:
  print:
    disable_toc: false
```

禁用打印时显示文档目录
```yaml
params:
  print:
    disable_toc: true
```




#### katex
> katex支持数学表达式，站点用：math: true
> mhchem支持化学式，站点用：chem: true
>


```yaml
params:
  katex:
    enable: true
    html_dom_element: document.body
    options:
      throwOnError: false
      errorColor: "#CD5C5C"
      delimiters:
        - left: $$
          right: $$
          display: true
        - left: $
          right: $
          display: false
        - left: \(
          right: \)
          display: false
        - left: \[
          right: \]
          display: true
    mhchem:
      enable: true

```

#### mermaid
> 图标支持：


```yaml
params:
  mermaid:
    enable: true
    theme: forest
    flowchart:
      diagramPadding: 6



```



#### plantuml
> PlantUML是 Mermaid 的替代品，可让您快速创建 UML 图，包括序列图、用例图和状态图。


```yaml
params:
  plantuml:
    enable: true
    theme: default
    svg_image_url: https://www.plantuml.com/plantuml/svg/
    svg: true

```



#### markmap
> markmap是一个 Javascript 库，用于在浏览器中将简单的文本定义呈现给 MindMa


```yaml
params:
  markmap:
    enable: true

```


#### prism
> 默认Chroma 来突出显示代码块，下载地址：https://prismjs.com/download.html，下载你自己的 Prism JS 和 CSS 文件
>
> 存放位置：static/js/prism.js，static/css/prism.css

开启prism，且添加static/js/prism.js和static/css/prism.css
```yaml
prism_syntax_highlighting: true

```


关闭prism
```yaml
prism_syntax_highlighting: false

```


#### navbar_translucent_over_cover_disable


禁用导航栏半透明
```yaml
prism_syntax_highlighting: false

```


开启导航栏半透明
```yaml
prism_syntax_highlighting: false

```


#### versions
> 文档版本控制


```yaml
params:
  versions:
    - version: master
      url: 'https://master.kubeflow.org'
    - version: v0.2
      url: 'https://v0-2.kubeflow.org'
    - version: v0.3
      url: 'https://v0-3.kubeflow.org'
  url_latest_version: https://your-latest-doc-site.com
  version_menu: Releases
  archived_version: true
  version: 1.0.0
```


- url_latest_version: 确保 site 参数url_latest_version包含您想要引导读者访问的网站的 URL。在大多数情况下，这应该是您文档最新版本的 URL

- version： 将站点参数设置version为归档文档集的版本。例如，如果归档文档为版本 0.1

- version_menu：版本下拉菜单的默认标题是Releases









#### navbar_logo
> 导航栏中出现徽标
>
> 存放位置：assets/icons/logo.svg
>
> 免费logo下载地址：[https://icons8.com/icon/set/logo/office](https://icons8.com/icon/set/logo/office)



开启显示
```yaml
params:
  ui:
    navbar_logo: true

```


禁止显示
```yaml
params:
  ui:
    navbar_logo: false

```



#### sidebar_search_disable
> 是否禁用侧边栏搜索框，建议关闭

禁用侧边栏搜索框
```yaml
params:
  ui:
    sidebar_search_disable: true

```













