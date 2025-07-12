---
title: 多面手
description: 多面手
date: 2023-10-30
weight: 10000


---

{{< alert >}}



{{< /alert >}}


【】某寝室共$13$人，其中$3$人只会玩《和平精英》，$5$人只会玩《王者荣耀》，其余$5$人《和平精英》和《王者荣耀》都会玩，现要从$13$人选出$9$个人代表寝室出战，$4$人组队玩《和平精英》，$5$人组队玩《王者荣耀》，共有多少种方案？

【**思路**】
- 元素位置：元素是$13$个不同的人；位置是《和平精英》和《王者荣耀》；但是玩《和平精英》的$4$人之间没有顺序，玩《王者荣耀》的$5$人之间没有顺序；
- 放置规则：从$13$人选出$9$个人，其中$4$人一组玩《和平精英》，$5$人一组队玩《王者荣耀》

【**解析**】方法一：按照$9$个人中只会玩《和平精英》的人数为分类标准：
- $\textcircled{1}$只会玩《和平精英》的人数为$0$时，则有：$\operatorname{C} _ {3}^{0}\operatorname{C} _ {5}^{4}\operatorname{C} _ {6}^{5}=30$；
- $\textcircled{2}$只会玩《和平精英》的人数为$1$时，则有：$\operatorname{C} _ {3}^{1}\operatorname{C} _ {5}^{3}\operatorname{C} _ {7}^{5}=630$；
- $\textcircled{3}$只会玩《和平精英》的人数为$2$时，则有：$\operatorname{C} _ {3}^{2}\operatorname{C}_{5}^{2}\operatorname{C} _ {8}^{5}=1680$；
- $\textcircled{4}$只会玩《和平精英》的人数为$3$时，则有：$\operatorname{C} _ {3}^{3}\operatorname{C} _ {5}^{1}\operatorname{C} _ {9}^{5}=630$；

综上，共有：$30+630+1680+630=2970$种方案

方法二：按照《和平精英》和《王者荣耀》都会玩的$5$人中去玩《和平精英》的人数为分类标准：
不妨设既会《和平精英》又会《王者荣耀》的$5$人为集合$A$
- $\textcircled{1}$$A$中去玩《和平精英》的人数为$1$时，则有：$\operatorname{C} _ {3}^{3}\operatorname{C} _ {5}^{1}\operatorname{C} _ {9}^{5}=630$；
- $\textcircled{3}$$A$中去玩《和平精英》的人数为$2$时，则有：$\operatorname{C}_ {3}^{2}\operatorname{C} _ {5}^{2}\operatorname{C} _ {8}^{5}=1680$；
- $\textcircled{3}$$A$中去玩《和平精英》的人数为$3$时，则有：$\operatorname{C} _ {3}^{1}\operatorname{C} _ {5}^{3}\operatorname{C} _ {7}^{5}=630$；
- $\textcircled{4}$$A$中去玩《和平精英》的人数为$4$时，则有：$\operatorname{C} _ {3}^{0}\operatorname{C} _ {5}^{4}\operatorname{C} _ {6}^{5}=30$；

综上，共有：$30+630+1680+630=2970$种方案

