---
title: eval
description: eval
date: 2025-04-01
weight: 30000
viz: true

---

{{< alert >}}



{{< /alert >}}


{{<alert>}}


{{</alert>}}



### eval


eval 属 Shell 内建命令，通过连接参数构造命令。

使用空格分隔每个参数，构造的命令应由 Shell 读取和执行。

eval 会对后面的命令进行两遍扫描。如果第一遍扫描后，命令是个普通命令，则执行此命令；如果命令中含有变量的间接引用，则保证间接引用的语义。也就是说，eval 命令将会首先扫描命令行进行所有的置换，然后再执行该命令。

主要是每次执行一个shell命令它会先检察一次，看到有`$`标志就会把值替换一次，然后再执行一遍。eval不会唤起起另一个shell来执行，而是在本身这个shell内多解析一次，所以替换的结果可以保留下来使用。

```bash
for i in `seq 1 $(cat /proc/cpuinfo |grep "physical id" |wc -l)`; do dd if=/dev/zero of=/dev/null & done
```

## 延迟变量展开


```bash
eval $ac_prev=$ac_option

```

- 延迟变量展开
```bash
eval $ac_prev=\$ac_option

eval enable_$ac_useropt=\$ac_optarg



```


在 Shell 脚本中，`eval $ac_prev=\$ac_option` 的 **反斜杠 `\`** 是为了 **延迟变量展开**，确保 `$ac_option` 的值在 `eval` 执行时被正确解析，而不是在命令构造阶段被提前展开。以下是详细解释：

---

### **关键作用**
1. **防止变量值被提前展开**：
   - 如果直接写成 `eval $ac_prev=$ac_option`，Shell 会在执行 `eval` **之前** 先展开 `$ac_prev` 和 `$ac_option`，可能导致语法错误或意外行为。
   - 通过转义 `\$ac_option`，`$` 符号会被保留为字面量，直到 `eval` 执行时才展开 `$ac_option`。

2. **处理特殊字符**：
   - 若 `ac_option` 的值包含空格、引号、分号等特殊字符，提前展开可能导致命令被错误分割。
   - 延迟展开可以保留值的完整性，确保特殊字符被正确处理。

---

### **具体分析**
假设 `ac_prev="bindir"` 且 `ac_option="/usr/local/bin"`：
- **不加反斜杠**：
  ```sh
  eval $ac_prev=$ac_option
  ```
  - Shell 会先展开为：
    ```sh
    eval bindir=/usr/local/bin
    ```
  - 这看似正常，但如果 `ac_option` 包含空格（如 `"/usr/my dir"`），展开后会变成：
    ```sh
    eval bindir=/usr/my dir
    ```
    - `dir` 会被视为独立命令，导致语法错误。

- **加反斜杠**：
  ```sh
  eval $ac_prev=\$ac_option
  ```
  - Shell 先解析转义字符，得到：
    ```sh
    eval $ac_prev=$ac_option
    ```
  - 此时 `$ac_prev` 和 `$ac_option` **尚未展开**，而是作为字符串传递给 `eval`。
  - `eval` 执行时，实际运行的命令是：
    ```sh
    bindir=$ac_option
    ```
    - `$ac_option` 在此时展开，若值为 `"/usr/my dir"`，会保留为完整字符串。

---

### **示例场景**
假设 `ac_option` 的值为 `"hello world"`：
1. **不加反斜杠**：
   ```sh
   eval $ac_prev=$ac_option
   → eval bindir=hello world
   ```
   - Shell 解析为 `bindir=hello` 和 `world`（后者被视为命令），导致错误。

2. **加反斜杠**：
   ```sh
   eval $ac_prev=\$ac_option
   → eval bindir=$ac_option
   ```
   - `eval` 执行时实际运行：
     ```sh
     bindir="hello world"  # 正确赋值
     ```

---

### **安全性与必要性**
- **防御特殊字符**：
  - 反斜杠确保 `$ac_option` 的值在 `eval` 内部展开，避免因空格、引号、分号等字符破坏命令结构。
- **动态变量名**：
  - `$ac_prev` 是动态变量名（如 `bindir`、`prefix`），必须通过 `eval` 间接赋值，而反斜杠是此模式的关键。

---

### **总结**
反斜杠 `\` 在此处的核心作用是：
1. **延迟 `$ac_option` 的展开**，确保它在 `eval` 内部解析。
2. **保护变量值的完整性**，防止特殊字符导致语法错误。
3. **支持动态变量名赋值**，是 Shell 脚本中间接引用的经典模式。











