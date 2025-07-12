---
title: 数组
description: array
date: 2023-10-30
weight: 200


viz: true
---

{{< alert >}}


{{< /alert >}}


## 分类
```viz-dot
digraph "数组" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "数组" -> "内置数组";
        "数组" -> "模板类vector";
        "数组" -> "模板类array";
        "数组" -> "模板类valarray";
}
```

## 声明
```c++
typeName arrayName[arraySize];

// arrayInt为int数组
int arrayInt[10];

// arrayFloat为float数组
int arrayFloat[10];


```

```viz-dot
digraph "数组" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "数组" -> "声明";
        "声明" -> "类型";
        "声明" -> "名称";
        "声明" -> "元素个数";
        "元素个数" -> "整型常数" [label="100"];
        "元素个数" -> "const常量" [label="const int NUM = 100"];
        "元素个数" -> "常量表达式" [label="8*sizeof (int)"];

        "数组" -> "初始化" [label="只能在定义时进行初始化"];
        "初始化" -> "可不指定元素个数" [label="int num[] = {1,2,3}"];
        "初始化" -> "元素可不全，但不能多" [label="int num[3] = {1,2}"];
        "初始化" -> "等号可忽略" [label="int num[3] {1,2,3}"];
        "初始化" -> "禁止缩窄转换" [label="char num[3] = {1,2,257}"];
        "初始化" -> "不能将数组赋值给数组" [label="array1 = array2"];

        "数组" -> "访问";
        "访问" -> "下标索引" [label="[0]~[len - 1]"];
        "访问" -> "不要越界";
}
```


## array.cpp

```c++
// array.cpp
#include <iostream>
const unsigned int NUM = 3;
int main() {
        using namespace std;
        // 在函数内声明但不初始化数组，则此时数组中的值是不确定的
        int yams[NUM];
        for (int i = 0; i < NUM; i++) {
                cout << "yams:" << i << ": " << yams[i] << endl;
        }

        for (int i = 0; i < NUM; i++) {
                yams[i] = i + 5;
        }
        cout << "Total yams = " << yams[0] + yams[1] + yams[2] << endl;

        // 声明并初始化数组
        int sb[NUM] = {20, 30, 5};
        for (int i = 0; i < NUM; i++) {
                cout << "sb:" << i << ": " << sb[i] << endl;
        }
        cout << "sizeof yams = " << sizeof yams << endl;
        cout << "sizeof yams[0] = " << sizeof yams[0] << endl;
        return 0;
}
```

输出：
```bash
yams:0: 4198576
yams:1: 0
yams:2: -11744
Total yams = 18
sb:0: 20
sb:1: 30
sb:2: 5
sizeof yams = 12
sizeof yams[0] = 4
```






















