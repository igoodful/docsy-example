---
title: 运算符
description: operator
date: 2023-10-30
weight: 200


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

## 整体
```viz-dot
digraph "my_init" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "运算符" -> "算术运算符" ;
        "算术运算符" -> "*" ;
        "算术运算符" -> "/" ;
        "算术运算符" -> " % " ;
        "算术运算符" -> "+" ;
        "算术运算符" -> "-" ;

        "运算符" -> "关系运算符";
        "关系运算符" -> ">" ;
        "关系运算符" -> ">=" ;
        "关系运算符" -> "<" ;
        "关系运算符" -> "<=" ;
        "关系运算符" -> "==" ;
        "关系运算符" -> "!=" ;

        "运算符" -> "逻辑运算符" ;
        "逻辑运算符" -> "&&" ;
        "逻辑运算符" -> "||" ;
        "逻辑运算符" -> "!" ;

        "运算符" -> "赋值运算符" ;
        "赋值运算符" -> "=" ;

        "运算符" -> "条件运算符" ;
        "条件运算符" -> "?" ;
        "运算符" -> "长度运算符";
        "长度运算符" -> "sizeof" [label="变量或类型"];



}
```

## 一、算术运算

#### 1. 除法

- 整数相除，余数丢弃
- 默认浮点型为double
- float相除

```c++
// divide.cpp
#include <iostream>
int main() {
        using namespace std;
        cout.setf(ios_base::fixed, ios_base::floatfield);
        cout << "Integer division: 9/5 = " << 9 / 5 << endl;
        cout << "Floating-point division: 9.0/5.0 = ";
        cout << 9.0 / 5.0 << endl;
        cout << "Mixed division: 9.0/5 = " << 9.0 / 5 << endl;
        cout << "double constants: 1e7/9.0 = ";
        cout << 1.e7 / 9.0 << endl;
        cout << "float constants: 1e7f/9.0f = ";
        cout << 1.e7f / 9.0f << endl;
        return 0;
}


```
输出：

```bash
Integer division: 9/5 = 1
Floating-point division: 9.0/5.0 = 1.800000
Mixed division: 9.0/5 = 1.800000
double constants: 1e7/9.0 = 1111111.111111
float constants: 1e7f/9.0f = 1111111.125000
```

#### 2. 求模

```c++
// modulus.cpp
#include <iostream>
int main() {
        using namespace std;
        const int Lbs_per_stn = 14;
        int lbs               = 100;
        int stone             = lbs / Lbs_per_stn;
        int pounds            = lbs % Lbs_per_stn;
        cout << lbs << " pounds are " << stone << " stone, " << pounds << " pound(s).\n";
        return 0;
}

```
输出：

```bash
100 pounds are 7 stone, 2 pound(s).
```




## 二、逻辑运算




## 三、关系运算符

- cout输出布尔值会自动将其转换成整数0和1
```c++
#include <iostream>
int main() {
        using namespace std;
        int x;

        cout << "The expression x = 100 has the value ";
        cout << (x = 100) << endl;
        cout << "Now x = " << x << endl;
        cout << "The expression x < 3 has the value ";
        cout << (x < 3) << endl;
        cout << "The expression x > 3 has the value ";
        cout << (x > 3) << endl;
        cout.setf(ios_base::boolalpha);
        cout << "The expression x < 3 has the value ";
        cout << (x < 3) << endl;
        cout << "The expression x > 3 has the value ";
        cout << (x > 3) << endl;
        return 0;
}
```

输出：
```
The expression x = 100 has the value 100
Now x = 100
The expression x < 3 has the value 0
The expression x > 3 has the value 1
The expression x < 3 has the value false
The expression x > 3 has the value true
```



## 四、赋值运算符

**赋值表达式的值**

将赋值表达式的值定义为左侧成员的值

```c++
mk = (pw = 10) + 5;
```
- (pw = 10)表达式的值为10
- mk=15


**从右向左结合**
```c++
x = y = z = 0;
```
- 首先将0赋值给z
- 再将z=0赋值给y
- 最后将y=0赋值给x


## sizeof


- 针对类型名称，需对类型名称添加括号
- 针对变量名称，括号是可选的




## 优先级与结合性

当两个运算符作用于同一个操作数时，才考虑优先级与结合性










