---
title: 39. valgrind
description: valgrind
date: 2025-04-19
weight: 39000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert>}}

- https://sourceware.org/pub/valgrind/valgrind-3.24.0.tar.bz2
- https://www.baeldung.com/linux/valgrind-install-uninstall

{{</alert>}}



## 1. gprof


```bash
yum -y install valgrind

```


## 2. baeldung_profiling.cpp

- `cat baeldung_profiling.cpp`

```c++

include <iostream>
#include <chrono>
#include <thread>

using namespace std;

// CPU-intensive task (factorial calculation)
unsigned long long factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

// Intentionally leaking memory for profiling
void memoryLeak() {
    new int[100]; // Not freeing memory on purpose
}

// Simulating an I/O-bound task (we're adding a delay)
void ioBoundTask() {
    this_thread::sleep_for(chrono::seconds(1));
}

int main() {
    cout << "Baeldung Profiling Test" << endl;
    auto start = chrono::high_resolution_clock::now();

    // Running CPU-intensive task
    cout << "Factorial(25) = " << factorial(25) << endl;

    // Simulating memory leak
    memoryLeak();

    // Simulating an I/O-bound task
    ioBoundTask();

    auto end = chrono::high_resolution_clock::now();
    cout << "Total Execution Time: "
         << chrono::duration<double>(end - start).count() << " seconds" << endl;

    return 0;
}
```


#### 编译


```bash
g++ -g -o baeldung_profiling baeldung_profiling.cpp

```

此时，我们可以使用 Valgrind 提供的七种工具中的任何一种来分析 C++ 代码。为了演示，我们使用 Memcheck 和 Massif。

具体来说，我们利用 Valgrind Memcheck 来检测baeldung_profiling程序中的内存泄漏和错误



#### 检测内存泄漏和错误

```bash
valgrind --leak-check=full --show-leak-kinds=all ./baeldung_profiling

```




#### 进行堆分析和内存使用情况分析

```bash
valgrind --tool=massif ./baeldung_profiling

```
因此，成功执行后，上述命令将生成一个名为massif.out.<pid>的输出文件，其中<pid>是执行程序的进程 ID。

最后，我们可以使用其 PID 查看和分析来自 Massif 输出文件或其他特定文件的堆内存使用情况的可读摘要。为此，我们通过执行ms_print检查所有文件的可读结果：

```bash
ms_print massif.out.*

```
在这里，我们可以在文件中观察结果：










