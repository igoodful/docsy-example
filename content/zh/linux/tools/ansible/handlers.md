---
title: handlers
description: handlers
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
- [handlers](https://www.junmajinlong.com/ansible/8_learn_back/index.html)
{{< /alert >}}

{{< alert color="secondary" >}}


{{< /alert >}}




### 通过--extra-vars或-e选项来传递key=value变量




```bash
ansible-playbook release.yml -e "user=igoodful"

ansible-playbook arcade.yml -e '{"pacman":"mrs","ghosts":["inky","pinky","clyde","sue"]}'
```






























