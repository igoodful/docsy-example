---
title: file
description: 文件测试
date: 2023-10-31
weight: 2000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert  color="primary">}}

{{< /alert >}}


## 1. 测试文件

Ansible提供了测试文件的相关函数：

- is exists：是否存在
- is directory：是否是目录
- is file：是否是普通文件
- is link：是否是软链接
- is abs：是否是绝对路径
- is same_file(F)：是否和F是硬链接关系
- is mount：是否是挂载点



```yaml
- debug:
    msg: "path is a directory"
  when: mypath is directory

# 如果mypath是绝对路径，即is测试返回true，
# 则筛选器返回absolute，否则返回relative
- debug:
    msg: "path is {{ (mypath is abs)|ternary('absolute','relative')}}"

- debug:
    msg: "path is the same file as path2"
  when: mypath is same_file(path2)

- debug:
    msg: "path is a mount"
  when: mypath is mount
```






