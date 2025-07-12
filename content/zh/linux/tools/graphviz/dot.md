---
title: dot
description: dot
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
- [graphviz](https://graphviz.org/docs/attrs/rankdir/)
{{%/pageinfo%}}

{{<alert>}}

- 节点定义
- 边定义

{{</alert>}}



## 排列方向

{{<alert>}}
rankdir:
- LR 从左到右
- RL 从右到左
- TB 从上到下
- BT 从下到上
{{</alert>}}

#### 1. 水平
> `rankdir="LR";`
```json
digraph "cmake" {
node [
  fontsize = "12"
  rankdir="LR";
];

    "node1" [ label = "源文件", shape = octagon ];
    "node2" [ label = "CMakeLists.txt", shape = octagon ];
    "node3" [ label = "CMake", shape = septagon ];
    "node4" [ label = "Makefile", shape = septagon ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = septagon ];
    "node6" [ label = "gcc/g++", shape = septagon ];
    "node7" [ label = "exe/lib", shape = septagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```

```viz-dot
digraph "cmake" {
node [
  fontsize = "12"
];
    rankdir="LR";
    "node1" [ label = "源文件", shape = octagon ];
    "node2" [ label = "CMakeLists.txt", shape = octagon ];
    "node3" [ label = "CMake", shape = septagon ];
    "node4" [ label = "Makefile", shape = septagon ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = septagon ];
    "node6" [ label = "gcc/g++", shape = septagon ];
    "node7" [ label = "exe/lib", shape = septagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```

#### 2. 竖直
> `rankdir="TB";`

```json
digraph "cmake" {
node [
  fontsize = "12"
];
    rankdir="TB";
    "node1" [ label = "源文件", shape = octagon ];
    "node2" [ label = "CMakeLists.txt", shape = octagon ];
    "node3" [ label = "CMake", shape = septagon ];
    "node4" [ label = "Makefile", shape = septagon ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = septagon ];
    "node6" [ label = "gcc/g++", shape = septagon ];
    "node7" [ label = "exe/lib", shape = septagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```

```viz-dot
digraph "cmake" {
node [
  fontsize = "12"
];
    rankdir="TB";
    "node1" [ label = "源文件", shape = octagon ];
    "node2" [ label = "CMakeLists.txt", shape = octagon ];
    "node3" [ label = "CMake", shape = septagon ];
    "node4" [ label = "Makefile", shape = septagon ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = septagon ];
    "node6" [ label = "gcc/g++", shape = septagon ];
    "node7" [ label = "exe/lib", shape = septagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```




## 节点形状

{{<alert>}}
shape:
- box
- polygon
- ellipse
- oval
- circle
- point
- egg
- triangle
- plaintext
- plain
- diamond
- trapezium
- parallelogram
- house
- pentagon
- hexagon
- septagon
- octagon
- doublecircle
- doubleoctagon
- tripleoctagon
- invtriangle
- invtrapezium
- invhouse
- Mdiamond
- Msquare
- Mcircle
- rect
- rectangle
- square
- star
- none
- underline
- cylinder
- note
- tab
- folder
- box3d
- component
- promoter
- cds
- terminator
- utr
- primersite
- restrictionsite
- fivepoverhang
- threepoverhang
- noverhang
- assembly
- signature
- insulator
- ribosite
- rnastab
- proteasesite
- proteinstab
- rpromoter
- rarrow
- larrow
- lpromoter
{{</alert>}}

#### shape
- [shape gitlab](https://gitlab.com/graphviz/graphviz/-/blob/main/doc/infosrc/shapelist#L0)
- [shape](https://graphviz.org/docs/attrs/shape/)

举例说明：
```viz-dot
digraph "cmake" {
node [
  fontsize = "12"
];
    rankdir="LR";
    "node1" [ label = "源文件", shape = box ];
    "node2" [ label = "CMakeLists.txt", shape = polygon ];
    "node3" [ label = "CMake", shape = ellipse ];
    "node4" [ label = "Makefile", shape = house ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = circle ];
    "node6" [ label = "gcc/g++", shape = octagon ];
    "node7" [ label = "exe/lib", shape = doubleoctagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```

源码如下:
```json
digraph "cmake" {
node [
  fontsize = "12"
];
    rankdir="LR";
    "node1" [ label = "源文件", shape = box ];
    "node2" [ label = "CMakeLists.txt", shape = polygon ];
    "node3" [ label = "CMake", shape = ellipse ];
    "node4" [ label = "Makefile", shape = house ];
    "node1" -> "node2";
    "node1" -> "node4";
    "node2" -> "node3";
    "node3" -> "node4";
    "node5" [ label = "make", shape = circle ];
    "node6" [ label = "gcc/g++", shape = octagon ];
    "node7" [ label = "exe/lib", shape = doubleoctagon ];
    "node4" -> "node5";
    "node5" -> "node6";
    "node6" -> "node7";
}
```




## 箭头方向

{{<alert>}}
dir:
- forward 正向箭头
- back 反向箭头
- both 双向箭头
- none 没有箭头
{{</alert>}}

```viz-dot
digraph {
  A->B [dir=forward]
  C->D [dir=back]
  E->F [dir=both]
  G->H [dir=none]
}
```

源码：
```json
digraph {
  A->B [dir=forward]
  C->D [dir=back]
  E->F [dir=both]
  G->H [dir=none]
}
```


```viz-dot
digraph G {
  subgraph cluster_yellow {
    a [color="red"]
    b [color="green"]
  }
}

```

















