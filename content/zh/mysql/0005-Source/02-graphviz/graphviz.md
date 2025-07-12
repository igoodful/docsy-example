---
title: graphviz
description: graphviz
date: 2023-11-17
weight: 300000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}

## 一、安装

```bash
yum -y install graphviz

pip install graphviz
```





## 二、使用

{{<alert>}}



{{</alert>}}



```bash
dot -version




```


设置分辨率，默认分辨率很低
```bash
dot -T png -Gdpi=300 mysql.dot -o mysql.png
```




## vscode
> graphviz
- Graphviz Markdown Preview


在命令面板中运行第一个插件提供的命令`graphviz interactive: preview (beside)`，会在右侧预览图形



```viz-dot
digraph "zlib" {
node [
        rankdir=LR;
        fontsize = "12"
];
    "node15" [ label = "zlib", shape = octagon ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
}
```

