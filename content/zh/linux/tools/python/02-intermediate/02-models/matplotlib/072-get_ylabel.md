---
title: 72. 获取Y轴标签
description: Axes.get_ylabel
weight: 720
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.get_ylabel


### 1. 参数




### 2. 使用



```python


```


### 3. 源码
```python
    def get_ylabel(self):
        label = self.yaxis.get_label()
        return label.get_text()
```




