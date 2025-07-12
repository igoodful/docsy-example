---
title: message
description: 向用户显示信息
date: 2023-11-17
weight: 201


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



{{< /alert >}}

语法：`message([<mode>] "message to display" ...)`

{{<alert>}}
<mode> 可选的参数，指定消息的类型。可能的值包括：
- STATUS: 显示一般信息。
- WARNING: 显示警告信息，但不会停止 CMake 的配置或生成过程。
- AUTHOR_WARNING: 类似于 WARNING，但通常用于项目作者的特定警告。
- SEND_ERROR: 显示错误信息并继续运行，但会跳过生成步骤。当使用 SEND_ERROR 时，CMake 会尝试继续运行以便收集更多的错误信息，但是不会尝试去生成项目。
- FATAL_ERROR: 显示错误信息并立即终止所有 CMake 处理。使用 FATAL_ERROR 会立即停止 CMake 的进程。
- "message to display" 是要显示的消息文本。
- ... 表示可以附加多个消息字符串，CMake 会自动将它们连接起来。
{{</alert>}}


```cmake

# 显示常规状态信息
message(STATUS "This is a STATUS message.")

# 显示警告信息，CMake处理会继续
message(WARNING "This is a WARNING message.")

# 显示开发者警告信息，CMake处理会继续
message(AUTHOR_WARNING "This is an AUTHOR_WARNING message.")

# 显示错误信息，CMake处理会继续，但不会生成任何内容
message(SEND_ERROR "This is a SEND_ERROR message.")

# 显示致命错误信息，CMake处理会立即停止
message(FATAL_ERROR "This is a FATAL_ERROR message.")

```

