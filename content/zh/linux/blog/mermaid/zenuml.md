---
title: zenuml
description: 基于 JavaScript 的图表工具，可呈现 Markdown 启发的文本定义以动态创建和修改图表。
date: 2023-10-16
weight: 100


---
{{< alert >}}
- [meimaid](https://cdn.jsdelivr.net/npm/mermaid@10.6.0/)
{{< /alert >}}


{{<note>}}
zenuml
{{</note>}}

```sql

zenuml
    title Demo
    Alice->John: Hello John, how are you?
    John->Alice: Great!
    Alice->John: See you later!


zenuml
    title Annotators
    @Actor Alice
    @Database Bob
    Alice->Bob: Hi Bob
    Bob->Alice: Hi Alice


zenuml
    title Sync message
    A.SyncMessage
    A.SyncMessage(with, parameters) {
      B.nestedSyncMessage()
    }



zenuml
    Alice->John: Hello John, how are you?
    while(true) {
      John->Alice: Great!
    }
```








