---
title: mermaid
description: mermaid
date: 2025-05-20
weight: 50
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## mermaid

- https://soft.xiaoshujiang.com/docs/grammar/feature/mermaid/
- https://mermaid.js.org/

#### 类图

```mermaid
 classDiagram
      Animal <|-- Duck
      Animal <|-- Fish
      Animal <|-- Zebra
      Animal : +int age
      Animal : +String gender
      Animal: +isMammal()
      Animal: +mate()
      class Duck{
          +String beakColor
          +swim()
          +quack()
      }
      class Fish{
          -int sizeInFeet
          -canEat()
      }
      class Zebra{
          +bool is_wild
          +run()
      }

```

#### 流程图

在 graph 的后方添加流程图的显示方向

- TB -从上到下
- BT - 从下到上
- RL - 从右到左
- LR - 从左到右
- TD - 跟 TB 一样，从上到下

```mermaid
graph LR
    A(员工发起审批)--> B{经理是否审批}
    B --> |同意| C[财务报销]
    B --> |拒绝| A
    C --> E(结束)

```

#### 状态图


```mermaid
stateDiagram-v2
    [*] --> Still
    Still --> [*]

    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]

```


#### 序列图

```mermaid
sequenceDiagram
    actor user as 小明
    participant client as Outlook 客户端
    participant website as Azure Blob storage
    participant aad as Azure Active Directory
    participant sbux as Starbucks backend service

    user ->> client : 打开星巴克插件
    client->>+ website : 访问网页
    website-->>- user : 要求登录
    user->>+ aad : 身份验证
    aad-->>- website : 授权成功
    user->>+ website : 门店查询
    website-->> sbux : 接口调用
    sbux -->> website : 返回门店列表
    website -->> user : 返回

```


#### 甘特图

```mermaid
gantt
    title 项目进度
    dateFormat  YYYY-MM-DD
    axisFormat  %m-%d
    excludes sunday,monday,tuesday,friday,2014-03-01
    section 第一阶段
    需求分析           :crit, a1, 2014-01-01, 30d
    设计评审           :after a1, 20d

    section 第二阶段
    第一批测试      :done, 2014-01-12  , 12d
    第二批测试      :24d
    客户验收        :milestone,2h

```

```mermaid
gantt
       dateFormat  YYYY-MM-DD
       title Adding GANTT diagram functionality to mermaid

       section A section
       Completed task            :done,    des1, 2014-01-06,2014-01-08
       Active task               :active,  des2, 2014-01-09, 3d
       Future task               :         des3, after des2, 5d
       Future task2              :         des4, after des3, 5d

       section Critical tasks
       Completed task in the critical line :crit, done, 2014-01-06,24h
       Implement parser and jison          :crit, done, after des1, 2d
       Create tests for parser             :crit, active, 3d
       Future task in critical line        :crit, 5d
       Create tests for renderer           :2d
       Add to mermaid                      :1d

       section Documentation
       Describe gantt syntax               :active, a1, after des1, 3d
       Add gantt diagram to demo page      :after a1  , 20h
       Add another diagram to demo page    :doc1, after a1  , 48h

       section Last section
       Describe gantt syntax               :after doc1, 3d
       Add gantt diagram to demo page      :20h
       Add another diagram to demo page    :48h

```


#### 用户行程图

```mermaid
journey
    title My working day
    section Go to work
      Make tea: 5: Me
      Go upstairs: 3: Me
      Do work: 1: Me, Cat
    section Go home
      Go downstairs: 5: Me
      Sit down: 5: Me

```


#### 饼图

```mermaid
pie
    title Key elements in Product X
    "Calcium" : 42.96
    "Potassium" : 50.05
    "Magnesium" : 10.01
    "Iron" :  5

```




