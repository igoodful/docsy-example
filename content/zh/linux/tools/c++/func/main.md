---
title: main函数
description: main函数
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

## 带参main函数

```c++
#include <iostream>

int main(int argc, char **argv)
{
	while (argc-- > 0){
		printf("argc:%d,*argv++：%s\n", argc,*argv++);
	}
}
```
输出：
```bash
[root@dev /root/tmp] # ./a.out  1 2 3
argc:3,*argv++：./a.out
argc:2,*argv++：1
argc:1,*argv++：2
argc:0,*argv++：3

```


## 无参main函数
```c++
// sqrt.cpp -- using the sqrt() function

#include <iostream>
#include <cmath>  // or math.h

int main() {
        using namespace std;

        double area;
        cout << "Enter the floor area, in square feet, of your home: ";
        cin >> area;
        double side;
        side = sqrt(area);
        cout << "That's the equivalent of a square " << side << " feet to the side." << endl;
        cout << "How fascinating!" << endl;
        return 0;
}
```



