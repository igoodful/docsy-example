---
title: 变量
description: var
date: 2023-10-30
weight: 100


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- 所有变量和函数首次使用前都必须先声明
- 声明也叫定义
- c++程序文件后缀拓展名为：`.cc/.cpp/.cxx`
- c++头文件后缀拓展名为：`.h/无后缀`
{{< /alert >}}


```viz-dot
digraph "语句" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "语句" -> "声明语句" [label="int carrots;"];
        "声明语句" -> "确定所需内存大小";
        "声明语句" -> "给存储单元指定名称";
        "语句" -> "赋值语句" [label="carrots = 25;"];
}
```


## var




#### 局部变量
> 局部变量也叫自动变量
- 函数的参数
- 函数内定义的变量

```viz-dot
digraph "局部变量" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "局部变量" -> "函数形参" [label="从调用函数处获取值"];
        "局部变量" -> "函数中定义" [label="函数中内获取值"];
}
```


## 局部变量
```c++
// lotto.cpp
#include <iostream>
long double probability(unsigned numbers, unsigned picks);
int main() {
        using namespace std;
        double total, choices;
        cout << "Enter the total number of choices on the game card and\n"
                "the number of picks allowed:\n";
        while ((cin >> total >> choices) && choices <= total) {
                cout << "You have one chance in ";
                cout << probability(total, choices);
                cout << " of winning.\n";
                cout << "Next two numbers (q to quit): ";
        }
        cout << "bye\n";
        return 0;
}

// 函数形参局部变量
long double probability(unsigned numbers, unsigned picks) {
        // 函数体中自定义局部变量
        long double result = 1.0;
        long double n;
        unsigned p;

        for (n = numbers, p = picks; p > 0; n--, p--)
                result = result * n / p;
        return result;
}
```



## 注释
```viz-dot
digraph "注释" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "注释" -> "单行注释";
        "注释" -> "多行注释";
}
```

举例说明：
```c++
// 单行注释

#include <iostream>
int main()
{
        using namespace std;                      // 单行注释
        cout << "Come up and C++ me some time.";  // 单行注释
        cout << endl;                             // 单行注释
        cout << "You won't regret it!" << endl;   // 单行注释
        return 0;
}

/*
多行注释
make definitions visible
make definitions visible
make definitions visible
*/
```








