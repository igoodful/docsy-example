---
title: 边属性
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

{{<alert>}}



{{</alert>}}

## 文本

#### fontname
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
#### fontsize


#### fontcolor




## dir



## comment






