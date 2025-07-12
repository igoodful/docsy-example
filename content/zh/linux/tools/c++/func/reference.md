---
title: 引用
description: reference
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
引用变量是一个别名，也就是说，它是某个已存在变量的另一个名字。一旦把引用初始化为某个变量，就可以使用该引用名称或变量名称来指向变量

引用就是变量的别名，操作一个变量的引用也就相当于操作变量本身，这一点跟指针很类似
{{< /alert >}}

{{<alert>}}
C语言之中大量利用指针作为形参或者函数返回值，这是由于值拷贝会有很大的消耗（比如传入传出一个大的结构体）。所以在C++之中使用引用作为函数参数和返回值的目的和使用指针是一样的。而且形式上更加直观，所以C++提倡使用引用
{{</alert>}}



```viz-dot
digraph "func" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12";
                ];
        "引用" -> "希望修改传入的对象";
        "引用" -> "降低赋值大对象";

}
```


## 声明引用

`[类型标识符] &[引用名]=[目标变量名]`


```c++
int a; int &ra=a; //定义引用ra,它是变量a的引用，即别名

string food  = "Pizza";
string &meal = food;

cout << food << "\n";  // 输出 Pizza
cout << meal << "\n";  // 输出 Pizza
```

- &在此不是求地址运算，而是起标识作用
- 引用在声明时就必须对它进行初始化
- 引用声明完毕后，相当于目标变量名有两个名称，即该目标原名称和引用名，且不能再把该引用名作为其他变量名的别名。ra=1;等价于 a=1;
- 引用本身不是一种数据类型，因此引用本身不占存储单元，系统也不给引用分配存储单元。故：对引用求地址，就是对目标变量求地址。&ra与&a相等。
- 不能为数组建立引用，因为数组是由若干个元素组成，所以不能建立数组的别名。

**引用是否占用存储空间：**

在C++中，引用不会占用额外的存储空间。引用是另一个对象的别名，它们在内存中没有自己的存储空间，而是直接绑定到另一个对象上。因此，引用本身不占用存储空间，它只是对另一个对象的引用或别名。

当你创建一个引用并将其绑定到一个对象时，编译器会将引用的使用视为对原始对象的使用。这意味着在编译期间，引用被替换为对原始对象的访问。因此，使用引用并不会增加程序的存储空间消耗。


**引用初始化问题：**

在声明引用时，通常情况下必须对它进行初始化。这是因为引用本质上是另一个对象的别名，它必须引用某个已经存在的对象。如果在声明引用时没有进行初始化，编译器无法确定它应该引用哪个对象，因此会导致编译错误。

```c++
int x = 10;
int& ref1 = x; // 正确，引用ref1被初始化为引用x

int& ref2; // 错误，引用在声明时没有初始化
```

但是在函数的形参中，引用参数并不需要在声明时进行初始化，因为在函数调用时，实参会被绑定到引用形参上，从而进行初始化。

```c++
void foo(int& ref) {
    // 这里可以使用ref，因为在函数调用时，实参已经初始化了ref
}

int main() {
    int x = 10;
    foo(x); // 在这里调用foo函数，x将会初始化ref
    return 0;
}
```
在函数调用 foo(x) 中，实参 x 被绑定到形参 ref 上，从而初始化了引用。因此在函数的形参中，不需要在声明时进行初始化。



**不能添加引用的情况：**

以下是不能添加引用的情况的一些示例：

- 不能将非常量引用绑定到右值（临时对象）上：

```c++
int& ref = 42; // 错误，不能将非常量引用绑定到右值
```

- 空指针不能被引用：

```c++
int* ptr = nullptr;
int& ref = *ptr; // 错误，空指针不能被引用
```

- 不能创建指向函数的引用：

```c++
void func();
void(&funcRef)() = func; // 错误，函数类型不能被引用
```

- 不能创建指向数组的引用：

```c++
int arr[5];
int(&arrRef)[5] = arr; // 错误，数组类型不能被引用
```
- 不能创建引用的引用：

```c++
int x = 10;
int& ref = x;
int& refRef = ref; // 错误，不能创建引用的引用
```
不过有两个例外：类型别名和模板参数时可以间接定义引用的引用


引用的引用不能通过指针间接访问：

```c++
int x = 10;
int& ref = x;
int*& ptrRef = &ref; // 错误，引用的引用不能通过指针间接访问
```

- 不能创建引用的数组：

```c++
int x = 10;
int& ref = x;
int& refArr[] = {ref}; // 错误，不能创建引用的数组
```
这些都是不能创建引用的情况。引用必须绑定到一个已存在的对象，而不能是一个临时对象、空指针、函数、数组或其他引用。



因为引用不存在自己的地址，指针不能指向引用，但可以定义指针的引用



## 引用变量

```c++
// firstref.cpp
#include <iostream>
int main() {
        using namespace std;
        int rats     = 101;
        // 定义一个int类型的引用变量，指向int变量rats
        int& rodents = rats;

        cout << "rats = " << rats;
        cout << ", rodents = " << rodents << endl;
        rodents++;
        cout << "rats = " << rats;
        cout << ", rodents = " << rodents << endl;

        //
        cout << "rats address = " << &rats;
        cout << ", rodents address = " << &rodents << endl;
        return 0;
}
```

输出：
```bash
rats = 101, rodents = 101
rats = 102, rodents = 102
rats address = 0x7fffffffd134, rodents address = 0x7fffffffd134

```

## 引用变量别名不可更改

```c++
// secref.cpp
#include <iostream>
int main() {
        using namespace std;
        int rats     = 101;
        // 引用变量rodents始终是变量rats的别名
        int& rodents = rats;

        cout << "rats = " << rats;
        cout << ", rodents = " << rodents << endl;

        cout << "rats address = " << &rats;
        cout << ", rodents address = " << &rodents << endl;

        int bunnies = 50;
        // 此处rodents还是变量rats的别名，此时将bunnies的值赋值给引用变量rodents
        rodents     = bunnies;
        cout << "bunnies = " << bunnies;
        cout << ", rats = " << rats;
        cout << ", rodents = " << rodents << endl;

        cout << "bunnies address = " << &bunnies;
        cout << ", rodents address = " << &rodents << endl;
        return 0;
}
```
输出：
```bash
rats = 101, rodents = 101
rats address = 0x7fffffffd134, rodents address = 0x7fffffffd134
bunnies = 50, rats = 50, rodents = 50
bunnies address = 0x7fffffffd130, rodents address = 0x7fffffffd134

```




## 引用与指针

```c++
// swaps.cpp
#include <iostream>
void swapr(int& a, int& b);
void swapp(int* p, int* q);
void swapv(int a, int b);

int main() {
        using namespace std;
        int wallet1 = 300;
        int wallet2 = 350;

        cout << "wallet1 = $" << wallet1;
        cout << " wallet2 = $" << wallet2 << endl;

        // 引用
        cout << "Using references to swap contents:\n";
        swapr(wallet1, wallet2);
        cout << "wallet1 = $" << wallet1;
        cout << " wallet2 = $" << wallet2 << endl;

        // 指针
        cout << "Using pointers to swap contents again:\n";
        swapp(&wallet1, &wallet2);
        cout << "wallet1 = $" << wallet1;
        cout << " wallet2 = $" << wallet2 << endl;

        // 无效
        cout << "Trying to use passing by value:\n";
        swapv(wallet1, wallet2);
        cout << "wallet1 = $" << wallet1;
        cout << " wallet2 = $" << wallet2 << endl;
        return 0;
}

// 引用
void swapr(int& a, int& b)
{
        int temp;

        temp = a;
        a    = b;
        b    = temp;
}

// 指针
void swapp(int* p, int* q)
{
        int temp;

        temp = *p;
        *p   = *q;
        *q   = temp;
}

// 无效
void swapv(int a, int b)
{
        int temp;

        temp = a;
        a    = b;
        b    = temp;
}

```

输出：
```bash
wallet1 = $300 wallet2 = $350
Using references to swap contents:
wallet1 = $350 wallet2 = $300
Using pointers to swap contents again:
wallet1 = $300 wallet2 = $350
Trying to use passing by value:
wallet1 = $300 wallet2 = $350
```

## 常引用


```c++
// cubes.cpp -- regular and reference arguments
#include <iostream>
double cube(double a);
double refcube(double &ra);
double refcube_const(const double &ra);

int main() {
        using namespace std;
        double x = 2.0;

        cout << cube(x);
        cout << " = cube of " << x << endl;

        cout << refcube(x);
        cout << " = cube of " << x << endl;

        cout << refcube_const(x);
        cout << " = cube of " << x << endl;
        return 0;
}

// 按值传递，不修改实参。适合基本数值类型
double cube(double a) {
        a *= a * a;
        return a;
}

// 按引用传递，修改实参。适合结构和类
double refcube(double &ra) {
        ra *= ra * ra;
        return ra;
}

// 按引用传递，不许修改实参。适合结构和类
double refcube_const(const double &ra) {
        return ra * ra * ra;
}
```
输出：
```bash
8 = cube of 2
8 = cube of 8
512 = cube of 8
```

## 不能将表达式赋值给引用变量


```c++
#include <iostream>
double cube(double a);
double refcube(double &ra);
double refcube_const(const double &ra);

int main() {
        using namespace std;
        double x = 2.0;
        // 错误
        cout << refcube(x + 3.0);
        cout << " = cube of " << x << endl;

        return 0;
}
double refcube(double &ra) {
        ra *= ra * ra;
        return ra;
}
```














