---
title: replace into
description: replace into
date: 2023-10-12
weight: 60000


---

{{< alert >}}
本章内容通过一个例子说明 replace into 带来的潜在的数据质量风险,当涉及 replace into 操作的表含有自增主键时，主备切换后会造成数据覆盖等不一致的情况发生。
> [案例](https://opensource.actionsky.com/20221208-mysql/)
{{< /alert >}}


### 一、环境信息

{{< note >}}

| 名称          | 配置       |
|:--------------|:-----------|
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 64C        |
| Memory        | 128G       |
| Space         | 2.6T       |
| MySQL Version | 8.0.26     |

{{< /note >}}













## 四、总结
由于 replace into 操作在遇到主键冲突的时候会修改主键的值，所以如果业务逻辑强依赖自增 ID ，绝对不要用 replace ，普通环境也不建议这样用，因为 replace into 操作可能会导致主键的重新组织。







































