---
title: CPATH
description: CPATH、C_INCLUDE_PATH、CPLUS_INCLUDE_PATH
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

### **`CPATH`、`C_INCLUDE_PATH`、`CPLUS_INCLUDE_PATH` 的区别**

这三个环境变量都与编译器查找头文件（`.h` 文件）的路径相关，但它们的作用范围和适用场景有所不同。以下是它们的详细区别：

---

### **1. `CPATH`**
- **作用**：指定编译器查找头文件的路径，适用于 **C 和 C++** 语言。
- **优先级**：高于系统默认的头文件路径（如 `/usr/include`）。
- **适用场景**：当需要为 C 和 C++ 项目统一指定头文件路径时使用。
- **示例**：
  ```bash
  export CPATH=/usr/local/include:/opt/myproject/include
  ```
  - 编译器会在 `/usr/local/include` 和 `/opt/myproject/include` 中查找头文件。

---

### **2. `C_INCLUDE_PATH`**
- **作用**：专门用于 **C 语言** 的头文件查找路径。
- **优先级**：高于 `CPATH` 和系统默认的头文件路径。
- **适用场景**：当需要为 C 语言项目单独指定头文件路径时使用。
- **示例**：
  ```bash
  export C_INCLUDE_PATH=/usr/local/include
  ```
  - 编译器会在 `/usr/local/include` 中查找 C 语言的头文件。

---

### **3. `CPLUS_INCLUDE_PATH`**
- **作用**：专门用于 **C++ 语言** 的头文件查找路径。
- **优先级**：高于 `CPATH` 和系统默认的头文件路径。
- **适用场景**：当需要为 C++ 语言项目单独指定头文件路径时使用。
- **示例**：
  ```bash
  export CPLUS_INCLUDE_PATH=/usr/local/include/c++/11
  ```
  - 编译器会在 `/usr/local/include/c++/11` 中查找 C++ 语言的头文件。

---

### **4. 优先级比较**
编译器查找头文件的优先级顺序如下：
1. **`C_INCLUDE_PATH`**（仅 C 语言）
2. **`CPLUS_INCLUDE_PATH`**（仅 C++ 语言）
3. **`CPATH`**（C 和 C++ 语言）
4. **系统默认路径**（如 `/usr/include`）

---

### **5. 示例：综合使用**
假设我们有一个项目，C 语言的头文件在 `/opt/c/include`，C++ 语言的头文件在 `/opt/cpp/include`，同时有一个公共头文件路径 `/opt/common/include`：
```bash
export CPATH=/opt/common/include
export C_INCLUDE_PATH=/opt/c/include
export CPLUS_INCLUDE_PATH=/opt/cpp/include
```

- **C 语言编译**：查找顺序为 `/opt/c/include` → `/opt/common/include` → 系统默认路径。
- **C++ 语言编译**：查找顺序为 `/opt/cpp/include` → `/opt/common/include` → 系统默认路径。

---

### **6. 总结**
| **环境变量**         | **作用范围** | **优先级** | **适用场景**                   |
|----------------------|-------------|-----------|----------------------------|
| `CPATH`              | C 和 C++     | 中         | 统一指定 C 和 C++ 的头文件路径 |
| `C_INCLUDE_PATH`     | 仅 C         | 高         | 单独指定 C 语言的头文件路径    |
| `CPLUS_INCLUDE_PATH` | 仅 C++       | 高         | 单独指定 C++ 语言的头文件路径  |

通过合理配置这些环境变量，可以灵活管理 C 和 C++ 项目的头文件查找路径，确保编译过程顺利进行。



