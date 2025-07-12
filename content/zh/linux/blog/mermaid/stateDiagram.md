---
title: stateDiagram
description: 基于 JavaScript 的图表工具，可呈现 Markdown 启发的文本定义以动态创建和修改图表。
date: 2023-10-16
weight: 35


---
{{< alert >}}
- [meimaid](https://cdn.jsdelivr.net/npm/mermaid@10.6.0/)
{{< /alert >}}


{{<note>}}


{{</note>}}


```mermaid
stateDiagram
    [*] --> Still
    Still --> [*]

    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]
```


```mermaid
---
title: Simple sample
---
stateDiagram-v2
    [*] --> Still
    Still --> [*]

    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]
```



















