---
title: 2. 声明
date: 2024-09-12
weight: 20
description: 声明
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

1. 跨平台的。它可以运行在Windows、Mac和各种Linux/Unix系统上。

{{< /alert >}}


## 声明

```python
#!/bin/python3
# -*- coding: utf-8 -*-

```
- `#!/bin/python3`用于指定脚本运行时应该使用的解释器。在这里指定了 python3，意味着这个脚本会由 Python 3 解释器运行
- `# -*- coding: utf-8 -*-`这是一个编码声明，用于指定 Python 源文件的编码。它告诉解释器这个文件使用的是 UTF-8 编码，适合处理多字节字符，比如中文或其他非 ASCII 字符。
  - 在 Python 2 中尤为重要，因为默认编码是 ASCII。
  - 而在 Python 3 中，源文件的默认编码是 UTF-8，所以这行代码更多是为了向后兼容性。





