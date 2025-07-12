---
title: for语句
description: 循环
date: 2023-10-30
weight: 500


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

## 一、for循环

#### 1. 流程
> 执行循环体前都必须经过判断条件表达式

规范：
- for与括号之间空格
-

```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "定义变量" -> "设置变量初始值";
        "设置变量初始值" -> "循环判断";
        "循环判断" -> "循环体"[label="true"];
        "循环判断" -> "退出循环"[label="false"];
        "循环体" -> "修改循环变量";
        "修改循环变量" -> "循环判断";


}
```

| 步骤               | 作用           | 示例  |
|:-----------------|:-------------|:------|
| 第一步             | 定义变量       | int i |
| 第二步             | 设置变量初始值 | i=0   |
| 第三步             | 循环判断       | i<5   |
| 第四步             | 循环体         |       |
| 第五步             | 修改循环变量   | i++   |
| 重复第三步到第五步 | 重复           |       |


#### 2. 示例

###### 1. 示例一
```c++
#include <iostream>
int main() {
        using namespace std;
        int i;
        for (i = 0; i < 5; i++)
                cout << "C++ knows loops.\n";
        cout << "C++ knows when to stop.\n";
        return 0;
}
```
- 将整数0转化为false
- 将非零转化为true

C++修改了上述C的写法，可以在for中声明变量，但该变量只能在循环中使用，循环结束后消失，但老旧的C++中此变量仍然可用
```c++
#include <iostream>
int main() {
        using namespace std;
        for (int i = 0; i < 5; i++)
                cout << "C++ knows loops.\n";
        cout << "C++ knows when to stop.\n";
        return 0;
}
```

###### 2. 示例三
```c++
#include <iostream>
const int ArSize = 16;
int main() {
        long long factorials[ArSize];
        factorials[1] = 1LL;
        factorials[0] = 1LL;
        for (int i = 2; i < ArSize; i++)
                factorials[i] = i * factorials[i - 1];
        for (int i = 0; i < ArSize; i++)
                std::cout << i << "! = " << factorials[i] << std::endl;
        return 0;
}
```

输出：
```c++
0! = 1
1! = 1
2! = 2
3! = 6
4! = 24
5! = 120
6! = 720
7! = 5040
8! = 40320
9! = 362880
10! = 3628800
11! = 39916800
12! = 479001600
13! = 6227020800
14! = 87178291200
15! = 1307674368000
```




## 二、while





## 三、do while



## 四、基于范围的for
> C++ 11开始支持

```c++
double prices[5] = {1.0, 2.0, 3.0, 4.0, 5.0};

for (double i : prices)
        cout<< x << endl;

for (double &i : prices)
        i = i + 2.0;

```



## 五、自增自减






#### 示例

```c++
// plus_one.cpp
#include <iostream>
int main() {
        using std::cout;
        int a = 20;
        int b = 20;

        cout << "a   = " << a << ":   b = " << b << "\n";
        cout << "a++ = " << a++ << ": ++b = " << ++b << "\n";
        cout << "a   = " << a << ":   b = " << b << "\n";
        return 0;
}
```

输出：
```c++
a   = 20:   b = 20
a++ = 20: ++b = 21
a   = 21:   b = 21
```




