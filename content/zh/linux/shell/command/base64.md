---
title: base64
description: base64
date: 2023-10-31
weight: 200000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}





## base64



#### 1. 加密

```bash
echo -n "root" | base64
```


#### 2. 解密

```bash
echo "cm9vdAo=" | base64 -d
```



#### 3. 加密文件内容

```bash
base64 file
```



#### 4. 解密文件内容

```bash
base64 -d file
```





