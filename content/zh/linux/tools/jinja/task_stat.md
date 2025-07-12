---
title: 任务状态
description: 测试任务状态
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


## 1. 测试任务状态

每个任务的执行结果都有4种状态：成功、失败、changed、跳过。

- succeeded、success
- failed、failure
- changed、change
- skipped、skip




```yaml
- shell: /usr/bin/foo
  register: result
  ignore_errors: True

- debug:
    msg: "it failed"
  when: result is failed

- debug:
    msg: "it changed"
  when: result is changed

- debug:
    msg: "it succeeded in Ansible >= 2.1"
  when: result is succeeded

- debug:
    msg: "it succeeded"
  when: result is success

- debug:
    msg: "it was skipped"
  when: result is skipped
```






