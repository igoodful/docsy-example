---
title: 函数
description: 函数
date: 2023-10-30
weight: 10


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

{{<alert>}}

{{</alert>}}

## 函数

对于系统的所有操作符，一般情况下，只支持基本数据类型和标准库中提供的class，对于用户自己定义的class，如果想支持基本操作，比如比较大小，判断是否相等，等等，则需要用户自己来定义关于这个操作符的具体实现。

```c++

```

## 函数重载

在同一个作用域内，可以声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同。您不能仅通过返回类型的不同来重载函数
- 参数个数不同
- 参数类型不同
- 参数顺序不同
- 返回类型不同（**错误**，返回值类型不在考虑范围内）




## 函数调用

#### 不要传入传出一个大的结构体
```viz-dot
digraph "函数调用" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        subgraph cluster_main {
                label="main()函数";
                "main() 开始" [shape=doubleoctagon];
                "return 结束" [shape=doubleoctagon];
                "main() 开始" -> "printf";
                "cout" -> "return 结束";
        };

        subgraph cluster_simple {
                label="simple()函数";
                "simple()" -> "sqrt";
                "sqrt" -> "abs";
        };

        "printf" -> "simple()";
        "abs" -> "cout";
}
```

```c++
// calling.cpp
#include <iostream>
#include <cmath>
using namespace std;
// 函数声明
void simple();

int main() {
        printf("%s","main() will call the simple() function:\n");
        // 函数调用
        simple();
        cout << "main() is finished with the simple() function.\n";
        return 0;
}

// 函数定义
void simple() {
        sqrt(10);
        abs(-20);
}
```



## 函数声明

```viz-dot
digraph "func" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "函数" -> "声明";
        "声明" -> "一般放在头文件" ;
        "声明" -> "放在程序文件开头" [label="方便该文件中其他函数调用，inline"];
}
```


```c++
// protos.cpp -- using prototypes and function calls
#include <iostream>
void cheers(int);       // prototype: no return value
double cube(double x);  // prototype: returns a double
int main() {
        using namespace std;
        cheers(5);  // function call
        cout << "Give me a number: ";
        double side;
        cin >> side;
        double volume = cube(side);  // function call
        cout << "A " << side << "-foot cube has a volume of ";
        cout << volume << " cubic feet.\n";
        cheers(cube(2));  // prototype protection at work
        return 0;
}

void cheers(int n) {
        using namespace std;
        for (int i = 0; i < n; i++)
                cout << "Cheers! ";
        cout << endl;
}

double cube(double x) {
        return x * x * x;
}
```



## 案例一

```c++
#include <iostream>
// 声明函数
void simon(int);

int main() {
        using namespace std;
        // 使用函数
        simon(3);
        cout << "Pick an integer: ";
        int count = 10;
        // 使用函数
        simon(count);
        cout << "Done!" << endl;
        return 0;
}

// 定义函数
void simon(int n){
        using namespace std;
        cout << "Simon says touch your toes " << n << " times." << endl;
}
```

输出：
```bash
Simon says touch your toes 3 times.
Pick an integer: Simon says touch your toes 10 times.
Done!
```


## 案例二

```c++
#include <iostream>
// 声明函数
int stonetolb(int);
int main() {
        using namespace std;
        int stone = 10;
        // 调用函数
        int pounds = stonetolb(stone);
        cout << stone << " stone = ";
        cout << pounds << " pounds." << endl;
        return 0;
}

// 定义函数
int stonetolb(int sts) {
        return 14 * sts;
}
```
输出：
```bash
10 stone = 140 pounds.
```


