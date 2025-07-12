---
title: 1. 初级阶段
description:  安装、配置
weight: 20000
---

{{< alert >}}
This is a placeholder page that shows you how to use this template site.
{{< /alert >}}


小Demo是这样的:
```python
Flag = True
if Flag:
    print ("我是真的")
else:
    print ("我是假的")
```


这时候会爆出一个:"Non-ASCII character '\xe6' in file"的错误,但是如果我将代码改成如下:
```python
Flag = True
if Flag:
    print 123
else:
    print 321
```


就没有问题,所以我猜测应该是编码的问题,在网上查了下答案,在第一行加上这样一句话:

```python
# encoding: utf-8
```

将编码格式改变为utf-8问题就解决了!
