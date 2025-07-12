---
title: graphviz
description: graphviz
date: 2023-11-17
weight: 3000


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
        fontsize = "12"
];
    rankdir="LR";
    "node15" [ label = "zlib", shape = octagon ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
}
```


#### 水平最小间距：nodesep


- nodesep=0.1

```bash
digraph {
    nodesep=0.1;
    node1; node2; node3;
}
```

```viz-dot
digraph {
    nodesep=0.1;
    node1; node2; node3;
}
```

- nodesep=0.5
```bash
digraph {
    nodesep=0.5;
    node1; node2; node3;
}
```

```viz-dot
digraph {
    nodesep=0.5;
    node1; node2; node3;
}
```

#### 垂直最小间距：ranksep
这是一个等级中的节点底部与下一等级中的节点顶部之间的最小垂直距离

- ranksep="0.5 equally"
```bash
digraph {
    ranksep="0.5 equally"
    node1 -> node2;
    node1 -> node3;
}
```

```viz-dot
digraph {
    ranksep="0.5 equally"
    node1 -> node2;
    node1 -> node3;
}
```
- ranksep="1.2 equally"
```bash
digraph {
    ranksep="1.2 equally"
    node1 -> node2;
    node1 -> node3;
}
```

```viz-dot

digraph {
    ranksep="1.2 equally"
    node1 -> node2;
    node1 -> node3;
}
```


#### 箭头大小：arrowsize
```c
digraph {
  quiver -> "0.5" [arrowsize=0.5]
  quiver -> "1"
  quiver -> "2" [arrowsize=2]
  quiver -> "3" [arrowsize=3]
}
```


```viz-dot
digraph {
  quiver -> "0.5" [arrowsize=0.5]
  quiver -> "1"
  quiver -> "2" [arrowsize=2]
  quiver -> "3" [arrowsize=3]
}
```

#### 布局：layout

- layout="dot"
```c
digraph G {
        layout="dot";
        N0 -> {N1; N2; N3; N4; N5; N6}
}
```

```viz-dot
digraph G {
        layout="dot";
        N0 -> {N1; N2; N3; N4; N5; N6}
}
```
- layout="neato"
```c
digraph G {
        layout="neato";
        N0 -> {N1; N2; N3; N4; N5; N6}
}
```

```viz-dot
digraph G {
        layout="neato";
        N0 -> {N1; N2; N3; N4; N5; N6}
}
```

#### 允许子图有边：compound

```c
digraph {
  compound=true;

  subgraph cluster_a {
    label="Cluster A";
    node1; node3; node5; node7;
  }
  subgraph cluster_b {
    label="Cluster B";
    node2; node4; node6; node8;
  }

  node1 -> node2 [label="1"];
  node3 -> node4 [label="2" ltail="cluster_a"];

  node5 -> node6 [label="3" lhead="cluster_b"];
  node7 -> node8 [label="4" ltail="cluster_a" lhead="cluster_b"];
}
```

```viz-dot
digraph {
  compound=true;

  subgraph cluster_a {
    label="Cluster A";
    node1; node3; node5; node7;
  }
  subgraph cluster_b {
    label="Cluster B";
    node2; node4; node6; node8;
  }

  node1 -> node2 [label="1"];
  node3 -> node4 [label="2" ltail="cluster_a"];

  node5 -> node6 [label="3" lhead="cluster_b"];
  node7 -> node8 [label="4" ltail="cluster_a" lhead="cluster_b"];
}
```

#### concentrate

- concentrate=false
```c
digraph {
    concentrate=false
    a -> b [label="1"]
    c -> b
    d -> b
}
```

```viz-dot
digraph {
    concentrate=false
    a -> b [label="1"]
    c -> b
    d -> b
}
```
- concentrate=true
```c
digraph {
    concentrate=true
    a -> b [label="1"]
    c -> b
    d -> b
}
```

```viz-dot
digraph {
    concentrate=true
    a -> b [label="1"]
    c -> b
    d -> b
}
```
#### 箭头类型：dir

```c
digraph {
  A->B [dir=forward]
  C->D [dir=back]
  E->F [dir=both]
  G->H [dir=none]
}
```

```viz-dot
digraph {
  A->B [dir=forward]
  C->D [dir=back]
  E->F [dir=both]
  G->H [dir=none]
}
```
