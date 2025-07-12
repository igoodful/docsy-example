---
title: debug
description: debug
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}

{{< alert color="secondary" >}}
参数
- msg
- var 它只能用来输出变量，而且var参数引用变量的时候，不能使用 {{}} 包围，因为var参数已经隐式地包围了一层 {{}}

{{< /alert >}}



```bash
# msg
ansible localhost -m debug -a 'msg="hello world"'

# msg
ansible localhost -e 'str=world' -m debug -a 'msg="hello {{str}}"'

# var
ansible localhost -e 'str="hello world"' -m debug -a 'var=str'
```














