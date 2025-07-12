---
title: 39. perf
description: perf
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



## 1. perf


```bash
yum -y install linux-tools-common linux-tools-generic linux-tools-$(uname -r)

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



#### perf record

```bash
perf record ./baeldung_profiling

```


#### perf report


```bash
perf report


```
