---
title: 40. process
description: process
date: 2025-04-19
weight: 400

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert>}}


{{</alert>}}



## 1. thread



#### `ps -eLf`

```bash
ps -eLf

```



#### 2. `ps -o nlwp`

```bash
ps -o nlwp 1250

ps -o thcount 1250

```

#### 3. `cat /proc/1250/status | grep Threads`

```bash
cat /proc/1250/status | grep Threads
```


#### `ls /proc/1250/task/`

```bash
ls /proc/1250/task/

```


#### `watch -n 1 ps -o thcount 1250`

```bash
watch -n 1 ps -o thcount 1250

```


#### `top`

- `top -> f -> d -> q`

```bash
top

```




