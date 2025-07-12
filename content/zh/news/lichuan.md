---
title: 利川
description: 利川
date: 2025-07-01
weight: 30000
viz: true

---
{{< alert >}}

{{< /alert >}}


{{<caution>}}
<!---->
利川市下辖2个街道、8个镇、4个乡：都亭街道、东城街道、谋道镇、柏杨坝镇、汪营镇、建南镇、忠路镇、团堡镇、毛坝镇、文斗镇、凉雾乡、元堡乡、南坪乡、沙溪乡，另有1个开发区：佛宝山生态综合开发区。305个村（社区）(其中262个村43个社区)。
{{</caution>}}

## 2023


```viz-dot
digraph "利川" {
rankdir = LR;
graph [nodesep=0.5 ranksep=0.4]
edge [
        fontsize="10"
];

node [
shape = rectangle style=filled fillcolor=lightblue fontname="Microsoft YaHei" fontsize = 10
];



"利川" -> "2街道"
"2街道" -> "都亭街道"
"2街道" -> "东城街道"

"利川" -> "8镇"
"8镇" -> "汪营镇"
"8镇" -> "建南镇"
"8镇" -> "谋道镇"
"8镇" -> "柏杨坝镇"
"8镇" -> "忠路镇"
"8镇" -> "团堡镇"
"8镇" -> "文斗镇"
"8镇" -> "毛坝镇"

"利川" -> "4乡"
"4乡" -> "凉雾乡"
"4乡" -> "元堡乡"
"4乡" -> "南坪乡"
"4乡" -> "沙溪乡"

"利川" -> "1开发区"
"1开发区" -> "佛宝山生态综合开发区"

}
```


```viz-dot
graph hierarchy {
rankdir = LR;
    graph [nodesep=0.5 ranksep=1.2]
    node [shape=rectangle style=filled fillcolor=lightblue fontname="Microsoft YaHei"]
    edge [arrowhead=none color=gray70]

    // 中心节点：谋道镇
    谋道镇 [shape=ellipse fillcolor=lightcoral fontsize=16 width=2 height=1.5]

    // 所有村节点（按名称排序）
    谋道镇 -- {
        node [width=1.8 height=0.4]
        "更新社区" "桃元村" "丰祝村" "南浦村" "相元村" "柏杨村" "大庄村"
        "光明村" "药材村" "万里村" "桂花村" "上磁村" "中台村" "响水村"
        "新进村" "兴隆村" "寨包梁村" "洞溪村" "东风村" "团结村" "德胜村"
        "牛角洞村" "太平村" "蚂蝗村" "长坪村" "寨坝村" "支罗村" "龙水村"
        "朝阳村" "白云村" "向阳村" "船头村" "青沙塘村" "富强村" "星光村"
        "中坝村" "铜锣村" "四合村" "沙帽村" "中山村" "农科村" "杨柳村"
        "鱼木村" "海螺口村" "古树嘴村" "茶园沟村" "中心村" "先锋村" "民生村"
        "铁峰村" "梨子园村" "鹿池村" "红光村" "堪金村" "双鹿村" "中槽村"
    }

    // 添加分组（可选）
    subgraph cluster_0 {
        style=invis
        "更新社区" "光明村" "星光村" "红光村" "中心村" "先锋村" "民生村"
    }
    subgraph cluster_1 {
        style=invis
        "南浦村" "中台村" "中坝村" "中山村" "中槽村"
    }
}
```
