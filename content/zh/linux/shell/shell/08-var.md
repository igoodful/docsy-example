---
title: 8. var
description: var
date: 2024-09-06
weight: 800
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>




## 变量长度
可计算出变量值${var}的长度

```bash
${#var}

```


## 变量是否定义、是否为空判断

```bash
if test ${ZSH_VERSION+y} && (emulate sh) >/dev/null 2>&1; then
	:
	emulate sh
	NULLCMD=:
	# Pre-4.2 versions of Zsh do word splitting on ${1+"$@"}, which
	# is contrary to our usage.  Disable this feature.
	alias -g '${1+"$@"}'='"$@"'
	setopt NO_GLOB_SUBST
else
	$as_nop
	case $( (set -o) 2>/dev/null) in #(
	*posix*)
		:
		set -o posix
		;; #(
	*)
		:
		;;
	esac
fi

```

在Shell脚本中，`${ZSH_VERSION+y}` 是一种 **参数扩展（Parameter Expansion）**，其作用是检查变量 `ZSH_VERSION` 是否被定义。它的行为可以总结如下：

---

### 语法含义
- **`${var+value}`** 表示：
  - 如果变量 `var` **已被定义**（无论其值是否为空），则替换为 `value`。
  - 如果 `var` **未被定义**，则替换为空字符串。

因此：
- **`${ZSH_VERSION+y}`** 的含义是：
  - 如果 `ZSH_VERSION` 已被定义（无论是否为空），则替换为 `y`。
  - 如果未定义，则替换为空字符串。

---

### 结合 `test` 命令的用途
在条件语句 `if test ${ZSH_VERSION+y} ...` 中：
- `test` 会检查其参数是否为非空。
- 如果当前 Shell 是 **Zsh**，则 `ZSH_VERSION` 会被自动定义（Zsh 的内置变量），此时 `${ZSH_VERSION+y}` 替换为 `y`。
- `test y` 结果为真，条件成立。
- 如果当前 Shell 不是 Zsh（例如 Bash 或 Dash），则 `ZSH_VERSION` 未定义，`${ZSH_VERSION+y}` 替换为空字符串，`test` 结果为假。

---

### 实际作用
这段代码的目的是 **检测当前 Shell 是否是 Zsh**：
1. **`${ZSH_VERSION+y}`** 检查是否存在 `ZSH_VERSION` 变量（Zsh 的标识）。
2. **`(emulate sh)`** 尝试让 Zsh 模拟 POSIX Shell（`sh`）的行为，并抑制输出（通过 `>/dev/null 2>&1`）。
3. **`if test ... && ...`** 组合条件：
   - 只有当前 Shell 是 Zsh **且** 能成功模拟 `sh` 时，才会执行后续的 Zsh 兼容性配置。

---

### 扩展分析
#### 为什么用 `${ZSH_VERSION+y}` 而不是 `${ZSH_VERSION}`？
- 如果直接使用 `${ZSH_VERSION}`，当 `ZSH_VERSION` 被定义为空字符串时，`test` 会认为条件不成立（空字符串视为假）。
- 但 `${var+value}` 的语法只关心变量是否被定义，不关心其值。因此无论 `ZSH_VERSION` 是否为空，只要 Zsh 环境存在，就能正确触发条件。

---

#### 类似语法对比
- **`${var:-value}`**：如果 `var` 未定义或为空，替换为 `value`。
- **`${var:+value}`**：如果 `var` 已定义且非空，替换为 `value`。
- **`${var+value}`**：如果 `var` 已定义（无论是否为空），替换为 `value`。

---

### 总结
- **`${ZSH_VERSION+y}`** 是一个巧妙的变量存在性检查。
- 结合 `test` 和 `emulate sh`，这段代码实现了以下逻辑：
  - 如果是 Zsh，则模拟 POSIX Shell 并配置兼容性选项。
  - 如果不是 Zsh，则尝试设置 POSIX 模式（例如在 Bash 中）。

这种写法常见于跨 Shell 兼容的脚本，用于处理不同 Shell 的差异性。
形式 　　　　　　　　     说明
- `${var}` 　　　　　　　 变量本来的值
- `${var:-word}` 　　　　 如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。
- `${var:=word}` 　　　　如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。
- `${var:?message}` 　　如果变量 var 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 var 是否可以被正常赋值。若此替换出现在Shell脚本中，那么脚本将停止运行。
- `${var:+word}`        如果变量 var 被定义，那么返回 word，但不改变 var 的值。