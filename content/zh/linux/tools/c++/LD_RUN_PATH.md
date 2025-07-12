---
title: LD_RUN_PATH
description: LD_RUN_PATH、LIBRARY_PATH、LD_LIBRARY_PATH
date: 2025-03-18
weight: 20
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}


---

### **`LIBRARY_PATH`、`LD_LIBRARY_PATH`、`LD_RUN_PATH` 的区别**

这三个环境变量都与库文件的查找路径相关，但它们的作用阶段和用途有所不同。以下是它们的详细区别：

---

### **1. `LIBRARY_PATH`**
- **作用**：指定 **链接器（ld）** 在 **编译时** 查找库文件（`.so` 或 `.a`）的路径。
- **适用阶段**：编译和链接阶段。
- **优先级**：高于系统默认的库文件路径（如 `/usr/lib`）。
- **适用场景**：在编译和链接时，指定额外的库文件路径。
- **示例**：
  ```bash
  export LIBRARY_PATH=/usr/local/lib:/opt/myproject/lib
  ```
  - 链接器会在 `/usr/local/lib` 和 `/opt/myproject/lib` 中查找库文件。

---

### **2. `LD_LIBRARY_PATH`**
- **作用**：指定 **动态链接器（ld.so）** 在 **运行时** 查找共享库（`.so`）的路径。
- **适用阶段**：程序运行时。
- **优先级**：高于系统默认的库文件路径（如 `/usr/lib`）。
- **适用场景**：在程序运行时，指定额外的共享库路径。
- **示例**：
  ```bash
  export LD_LIBRARY_PATH=/usr/local/lib:/opt/myproject/lib
  ```
  - 程序运行时会在 `/usr/local/lib` 和 `/opt/myproject/lib` 中查找共享库。

---

### **3. `LD_RUN_PATH`**
- **作用**：指定 **链接器（ld）** 在 **编译时** 将库路径嵌入到可执行文件中，供 **运行时** 使用。
- **适用阶段**：编译和链接阶段（影响运行时）。
- **优先级**：高于系统默认的库文件路径（如 `/usr/lib`）。
- **适用场景**：在编译时指定运行时库查找路径，避免运行时依赖 `LD_LIBRARY_PATH`。
- **示例**：
  ```bash
  export LD_RUN_PATH=/usr/local/lib
  ```
  - 编译后的可执行文件会记录 `/usr/local/lib` 作为运行时库查找路径。

---

### **4. 优先级与作用阶段对比**
| **环境变量**      | **作用阶段**               | **优先级** | **适用场景**                     |
|-------------------|-------------------------|-----------|------------------------------|
| `LIBRARY_PATH`    | 编译和链接阶段             | 中         | 指定编译时库文件路径             |
| `LD_LIBRARY_PATH` | 运行时                     | 高         | 指定运行时共享库路径             |
| `LD_RUN_PATH`     | 编译和链接阶段（影响运行时） | 高         | 指定运行时库路径并嵌入可执行文件 |

---

### **5. 示例：综合使用**
假设我们有一个项目，库文件在 `/opt/myproject/lib`，希望在编译时和运行时都能正确找到库文件：

#### **5.1 编译时**
```bash
export LIBRARY_PATH=/opt/myproject/lib
export LD_RUN_PATH=/opt/myproject/lib
gcc -o myprogram myprogram.c -lmyproject
```
- **`LIBRARY_PATH`**：确保链接器在编译时找到 `libmyproject.so`。
- **`LD_RUN_PATH`**：将 `/opt/myproject/lib` 嵌入可执行文件，供运行时使用。

#### **5.2 运行时**
如果未使用 `LD_RUN_PATH`，可以在运行时设置 `LD_LIBRARY_PATH`：
```bash
export LD_LIBRARY_PATH=/opt/myproject/lib
./myprogram
```

---

### **6. 总结**
| **环境变量**      | **作用阶段**               | **优先级** | **适用场景**                     |
|-------------------|-------------------------|-----------|------------------------------|
| `LIBRARY_PATH`    | 编译和链接阶段             | 中         | 指定编译时库文件路径             |
| `LD_LIBRARY_PATH` | 运行时                     | 高         | 指定运行时共享库路径             |
| `LD_RUN_PATH`     | 编译和链接阶段（影响运行时） | 高         | 指定运行时库路径并嵌入可执行文件 |

通过合理配置这些环境变量，可以确保程序在编译、链接和运行时都能正确找到所需的库文件。