---
title: 基本类型
description: 基本类型
date: 2023-10-30
weight: 50


viz: true
---

{{< alert >}}

- 一字节（byte）为8位比特（bit）
- 1 KB = 1024 byte
- 1 MB = 1024 KB
- 1 GB = 1024 MB
- 1 TB = 1024 GB


{{< /alert >}}



```viz-dot
digraph "基本类型" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "基本类型" -> "整型";
        "整型" -> "char" [label="256"];
        "char" -> "unsigned char" [label="0~256"];
        "char" -> "signed char" [label="-128~127"];

        "整型" -> "short" [label="6 5535"];
        "short" -> "unsigned short" [label="0~65535"];
        "short" -> "signed short" [label="-32768~32767"];

        "整型" -> "int" [label="42 9496 7296"];
        "整型" -> "long" [label="1844 6744 0737 0955 1616"];
        "整型" -> "long long";

        "基本类型" -> "浮点型";
        "浮点型" -> "float" [label="3.14F\n6或7位有效数字"];
        "浮点型" -> "double";
        "浮点型" -> "long double" [label="3.14L"];

        "基本类型" -> "字符型";
        "基本类型" -> "布尔";



}
```

## climits
```c++
// limits.cpp
#include <iostream>
#include <climits>
int main() {
        using namespace std;
        cout << "Bits per byte = " << CHAR_BIT << endl;
        cout << "short is " << sizeof(short) << " bytes." << endl;
        cout << "int is " << sizeof(int) << " bytes." << endl;
        cout << "long is " << sizeof(long) << " bytes." << endl;
        cout << "long long is " << sizeof(long long) << " bytes." << endl;
        cout << endl;

        cout << "Maximum values:" << endl;
        cout << "unsigned char max: " << UCHAR_MAX << endl;
        cout << "char min: " << CHAR_MIN << endl;
        cout << "char max: " << CHAR_MAX << endl;

        cout << "unsigned short max: " << USHRT_MAX << endl;
        cout << "short min: " << SHRT_MIN << endl;
        cout << "short max: " << SHRT_MAX << endl;

        cout << "unsigned int max: " << UINT_MAX << endl;
        cout << "int min: " << INT_MIN << endl;
        cout << "int max: " << INT_MAX << endl;

        cout << "unsigned long max: " << ULLONG_MAX << endl;
        cout << "long min: " << LONG_MIN << endl;
        cout << "long max: " << LONG_MAX << endl;

        cout << "long long: " << LLONG_MAX << endl << endl;
        return 0;
}

```


输出：
```bash
Bits per byte = 8
short is 2 bytes.
int is 4 bytes.
long is 8 bytes.
long long is 8 bytes.

Maximum values:
unsigned char max: 255
char min: -128
char max: 127
unsigned short max: 65535
short min: -32768
short max: 32767
unsigned int max: 4294967295
int min: -2147483648
int max: 2147483647
unsigned long max: 18446744073709551615
long min: -9223372036854775808
long max: 9223372036854775807
long long: 9223372036854775807
```



## 整型常量



```viz-dot
digraph "整型常量" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "整型常量" -> "十进制";
        "十进制" -> "int" [label="默认"];
        "十进制" -> "U" [label="unsigned"];
        "十进制" -> "L" [label="long"];
        "十进制" -> "LL" [label="long long"];
        "整型常量" -> "十六进制" [label="0x"];
        "十六进制" -> "cout<<hex;" [label="显示十六进制"];
        "整型常量" -> "八进制" [label="0|(1~7)"];
        "八进制" -> "cout<<oct;" [label="显示八进制"];
}
```



## 浮点型

- 小数点：3.14
- 科学计数法：3.14E8
- 默认都是double类型
- float常量：3.14F
- long double常量：3.14L



```c++
// floatnum.cpp
#include <iostream>
int main() {
        using namespace std;
        cout.setf(ios_base::fixed, ios_base::floatfield);
        float tub           = 10.0 / 3.0;
        double mint         = 10.0 / 3.0;
        const float million = 1.0e6;

        cout << "tub = " << tub << endl;
        cout << "million * tub = " << million * tub << endl;
        cout << "10 * million * tub = " << 10 * million * tub << endl;

        cout << "mint = " << mint << endl;
        cout << "million * mint = " << million * mint << endl;
        return 0;
}

```
输出：
```bash
tub = 3.333333
million * tub = 3333333.250000
10 * million * tub = 33333332.000000
mint = 3.333333
million * mint = 3333333.333333
```

#### 精度问题
float类型只有6位有效数字：

```c++
// fltadd.cpp
#include <iostream>
int main() {
        using namespace std;
        float a = 2.34E+22f;
        float b = a + 1.0f;

        cout << "a = " << a << endl;
        cout << "b - a = " << b - a << endl;
        return 0;
}


```
输出：
```bash

a = 2.34e+22
b - a = 0
```














