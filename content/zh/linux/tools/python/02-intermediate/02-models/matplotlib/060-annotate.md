---
title: 60. 添加注释
description: Axes.annotate
weight: 600
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.annotate

- [annotate](https://www.scaler.com/topics/matplotlib/arrow-in-matplotlib/)

### 1. 参数

| 参数            | 功能                                            | 默认值            |
|:----------------|:------------------------------------------------|:------------------|
| text            | 箭头尾部与注释一起位于xytext点                  | str               |
| xy              | 箭头位于xy点                                    | (float, float)    |
| xytext          | 文本开始位置(float, float)                      | xy                |
| xycoords        | xy所处的坐标系                                  | data              |
| textcoords      | xytext所处的坐标系                              | xycoords          |
| arrowprops      | 绘制箭头的属性                                  | dict \| None=None |
| annotation_clip | 当注释点xy位于轴区之外时，是否剪裁（即不绘制）注释 | bool \| None=None |
| `**kwargs`      |                                                 |                   |



**arrowprops**
- arrowstyle
  - `-`
  - `->`
  - `<-`
  - `<->`
  - `-|>`
  - `<|-`
  - `<|-|>`
  - `|-|`
  - `-[`
  - `fancy`
  - `simple`
  - `wedge`
- connectionstyle
-






**关键字参数**

| `**kwargs`             | 功能                                       | 默认值 |
|:-----------------------|:-------------------------------------------|:-------|
| fontsize               | 字体大小                                   |        |
| horizontalalignment/ha | left，center，right                          |        |
| verticalalignment/va   | baseline,bottom,top,center,center_baseline |        |
| color/c                |                                            |        |
|                        |                                            |        |
|                        |                                            |        |



**xycoords**

- data 基于实际xy坐标的值，最常见
- polar 极坐标，xy表示(角度，半径)
- axes points 基于轴的点，1 point=1/72 inch
- axes pixels 基于轴的像素，0表示轴左下角
- axes fraction 基于轴的相对尺寸，(0,0)表示轴区域左下角，(1,1)表示轴区域右上角
- figure points 基于图形中的点，1 point=1/72 inch
- figure pixels 基于图形中的像素，0表示图形左下角
- figure fraction 基于图形的相对尺寸，(0,0)表示图形左下角，(1,1)表示图形右上角
- subfigure points 基于子图的点
- subfigure pixels 基于子图的像素
- subfigure fraction 基于子图的相对尺寸


**textcoords**
- offset points  按照点偏移
- offset pixels  按照像素偏移
- offset fontsize 按照字体大小偏移


**arrowprops**
- facecolor
- shrink
- width
- headwidth
- headlength
- arrowstyle
- connectionstyle `[ 'arc3' | 'angle3' | 'angle' | 'arc' | 'bar' ]`
- relpos
- patchA
- patchB
- shrinkA
- shrinkB
- mutation_scale
- mutation_aspect




**comment_clip**
1. 如果为 True ，则当xy位于轴之外时，注释将被剪裁。
2. 如果为 False，则始终会绘制注释。
3. 如果为None ，则当xy位于轴之外并且xycoords为数据时，注释将被剪裁。






### 2. 使用



```python
# 箭头
ax.annotate('local max', xy=(2, 1), xytext=(3, 1.5), arrowprops=dict(facecolor='black', shrink=0.05))
ax.annotate('annotate', xy=(2, 1), xytext=(3, 4), arrowprops=dict(facecolor='black', shrink=0.05))
ax.annotate(f'{height}',xy=(rect.get_x() + rect.get_width() / 2, height),xytext=(0, 4),textcoords='offset points',ha='center', va='bottom')

ax.annotate('Increase',xy=(6,8),xytext=(2,4),arrowprops={})

```

绘制多组数据的方法有很多种
- 最直接的方法就是text多次调用
- x和/或y是二维数组
- 指定多组[x]、y、[fmt]组



### 3. 源码

```python
    @_docstring.dedent_interpd
    def annotate(self, text, xy, xytext=None, xycoords='data', textcoords=None, arrowprops=None, annotation_clip=None, **kwargs):
        # Signature must match Annotation. This is verified in
        # test_annotate_signature().
        a = mtext.Annotation(text, xy, xytext=xytext, xycoords=xycoords,
                             textcoords=textcoords, arrowprops=arrowprops,
                             annotation_clip=annotation_clip, **kwargs)
        a.set_transform(mtransforms.IdentityTransform())
        if kwargs.get('clip_on', False) and a.get_clip_path() is None:
            a.set_clip_path(self.patch)
        self._add_text(a)
        return a

```



