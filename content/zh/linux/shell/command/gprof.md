---
title: 39. gprof
description: gprof
date: 2025-04-19
weight: 39000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert>}}


{{</alert>}}



## 1. gprof


```bash
yum -y install binutils

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
g++ -g -pg -o baeldung_profiling baeldung_profiling.cpp

```
- `-pg` 我们可以使用-pg标志启用分析功能来编译baeldung_profiling.cpp。具体来说，  -p选项会选择prof分析器，因为它会生成合适的输出。
- `-g` 另一方面，  -g 选项会启用系统原生格式的常规调试信息。



#### gperf

```bash
gprof baeldung_profiling gmon.out > gprof_report.txt

cat gprof_report.txt

```
值得注意的是，我们可以查看函数调用统计和执行时间，这可以帮助开发人员提高程序性能。







