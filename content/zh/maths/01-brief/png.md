---
title: 图片标准
description: 图片标准
date: 2017-01-05
weight: 50
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 1. 参考

- 严格遵循宽高比例为黄金分割比例：1/0.618
- https://matplotlib.org/3.5.3/api/_as_gen/matplotlib.pyplot.gca.html
- 一本标准的高中课本通常是20厘米宽、28厘米高，厚度大约在2-3厘米之间
- 16开尺寸：长26厘米,宽18.4厘米；A4尺寸是：长29.7厘米,宽21厘米.A4尺寸比16开尺寸大一点点；
- 当前用python画图太慢了，改为geogebra画图，后期再集中改为python画图


## 2. 图片最终尺寸标准


1. 单张图片，题目中的图片，宽度6cm，比值为4:3，权衡美观与清晰度


2. 单张图片，理论中的图片，宽度8cm，比值为4:3，权衡美观与清晰度


3. 单张图片，特别突出、特别重要的图片，宽度4英寸，比值为4:3，权衡美观与清晰度


4. 多张图片，按照每张宽度6cm，且比值为1.618:1计算，保证美观


5. 图片格式均为svg，保证图片缩放不失真


6. 图片dpi均为300，保证图片清晰

7. 渐近线宽度1.5 ，线型为`--`

8. 坐标轴线宽度1 ，线型为`:`



## 3. 示例

- 单张图片，题目中的图片





- 单张图片，理论中的图片




- 单张图片，特别突出、特别重要的图片



- 多张图片




## 4. 其他尺寸

教科书：

- 单张图片宽度占总宽的0.236，高度占总长的0.110，那么换算成20厘米宽、28厘米高的话，4.7cm：3cm，换成英寸的话，1.85英寸：1.3875
-

1.85英寸：1.3875英寸

![/maths/single_plot.png](/maths/185.svg)

14

![/maths/single_plot.png](/maths/14.svg)


2.5英寸，4：3

![/maths/single_plot.png](/maths/25.svg)


2.5英寸，4：3 legendlength

![/maths/single_plot.png](/maths/legendlength.svg)


2.36英寸(6cm)，4：3 legendlength

![/maths/single_plot.png](/maths/236.svg)

6cm9font，4：3 legendlength

![/maths/single_plot.png](/maths/6cm9font.svg)

(8cm)，4：3 legendlength

![/maths/single_plot.png](/maths/8cm.svg)

(8cm)，4：3 legendlength

![/maths/single_plot.png](/maths/8cm10.svg)

2inc，4：3 legendlength

![/maths/single_plot.png](/maths/2inc.svg)




4:3

![/maths/single_plot.png](/maths/0.svg)


按照黄金分割比例

比例为：4：2.472

![/maths/single_plot.png](/maths/618.svg)

比例为：8：2.472

![/maths/single_plot.png](/maths/12618.svg)

比例为：4：4.944

![/maths/single_plot.png](/maths/21618.svg)

8:4.944

![/maths/single_plot.png](/maths/4618.svg)


2.35

![/maths/single_plot.png](/maths/235.svg)





## 5. 源码

```python
#!/usr/bin/env python
#!encoding=utf-8
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = 'Microsoft YaHei'

# 题目专用，且单张图片
def single_plot_timu():
        plt.figure(figsize=(6.0/2.54, 6.0*3/4/2.54))
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-1, 8, 100000)
        y = np.power(x,2) / np.exp(x)
        plt.plot(x, y, label=r'$y=f(x)$')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

# 理论专用，且单张图片
def single_plot_important():
        plt.figure(figsize=(8.0/2.54, 8.0*3/4/2.54))
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-1, 8, 100000)
        y = np.power(x,2) / np.exp(x)
        plt.plot(x, y, label=r'$y=f(x)$')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

# 大图专用，且单张图片
def single_plot_bold():
        plt.figure(figsize=(4, 3))
        plt.xticks(fontsize=10)
        plt.yticks(fontsize=10)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-1, 8, 100000)
        y = np.power(x,2) / np.exp(x)
        plt.plot(x, y, label=r'$y=f(x)$')
        plt.legend(loc = 0, prop = {'size':10}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_12():
        plt.figure(figsize=(6.0*2/2.54, 6.0/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)

        plt.subplot(1, 2, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 2, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_21():
        plt.figure(figsize=(6.0/2.54, 6.0*2/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)

        plt.subplot(2, 1, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 1, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_22():
        plt.figure(figsize=(6.0*2/2.54, 6.0*2/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)
        x3 = np.linspace(0, 2*np.pi, 100000)
        y3 = np.sin(x3) * np.power(x3,1)
        x4 = np.linspace(0, 2*np.pi, 100000)
        y4 = np.cos(x4) * np.power(x4,2)


        plt.subplot(2, 2, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 3)
        plt.plot(x3, y3, label=r'$y=\sin x \cdot x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 4)
        plt.plot(x4, y4, label=r'$y=\cos x \cdot x^2$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_14():
        plt.figure(figsize=(6.0*4/2.54, 6.0/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)
        x3 = np.linspace(0, 2*np.pi, 100000)
        y3 = np.sin(x3) * np.power(x3,1)
        x4 = np.linspace(0, 2*np.pi, 100000)
        y4 = np.cos(x4) * np.power(x4,2)


        plt.subplot(1, 4, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 3)
        plt.plot(x3, y3, label=r'$y=\sin x \cdot x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 4)
        plt.plot(x4, y4, label=r'$y=\cos x \cdot x^2$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()


if __name__ == "__main__":
        single_plot_timu()


```


### 20240915

```python
#!/usr/bin/env python
#!encoding=utf-8
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = 'Microsoft YaHei'

# 大图专用，且单张图片
def single_plot_bold():
        plt.figure(figsize=(4, 3))
        plt.xticks(fontsize=10)
        plt.yticks(fontsize=10)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-1, 8, 100000)
        y = np.power(x,2) / np.exp(x)
        plt.plot(x, y, label=r'$y=f(x)$')
        plt.legend(loc = 0, prop = {'size':10}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_12():
        plt.figure(figsize=(6.0*2/2.54, 6.0/2.54/1.618))

        x1 = np.linspace(0, 1.6, 100000)
        y1 = np.log(x1)*np.power(x1,2)
        x2 = np.linspace(0, 16, 100000)
        y2 = np.power(x2,2)/np.exp(x2) - 3.0/4.0

        plt.subplot(1, 2, 1)
        plt.plot(x1, y1, label=r'$f(x)$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 2, 2)
        plt.plot(x2, y2, label=r'$g(x)$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplots_adjust(wspace=0.5)
        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_21():
        plt.figure(figsize=(6.0/2.54, 6.0*2/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)

        plt.subplot(2, 1, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 1, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_22():
        plt.figure(figsize=(6.0*2/2.54, 6.0*2/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)
        x3 = np.linspace(0, 2*np.pi, 100000)
        y3 = np.sin(x3) * np.power(x3,1)
        x4 = np.linspace(0, 2*np.pi, 100000)
        y4 = np.cos(x4) * np.power(x4,2)


        plt.subplot(2, 2, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 3)
        plt.plot(x3, y3, label=r'$y=\sin x \cdot x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(2, 2, 4)
        plt.plot(x4, y4, label=r'$y=\cos x \cdot x^2$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

def multi_plot_timu_14():
        plt.figure(figsize=(6.0*4/2.54, 6.0/2.54/1.618))

        x1 = np.linspace(0, 2*np.pi, 100000)
        y1 = np.sin(x1)
        x2 = np.linspace(0, 2*np.pi, 100000)
        y2 = np.cos(x2)
        x3 = np.linspace(0, 2*np.pi, 100000)
        y3 = np.sin(x3) * np.power(x3,1)
        x4 = np.linspace(0, 2*np.pi, 100000)
        y4 = np.cos(x4) * np.power(x4,2)


        plt.subplot(1, 4, 1)
        plt.plot(x1, y1, label=r'$y=\sin x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 2)
        plt.plot(x2, y2, label=r'$y=\cos x$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 3)
        plt.plot(x3, y3, label=r'$y=\sin x \cdot x$')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.subplot(1, 4, 4)
        plt.plot(x4, y4, label=r'$y=\cos x \cdot x^2$',c='r')
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()


# 理论专用，且单张图片
def single_plot_important():
        plt.figure(figsize=(8.0/2.54, 8.0*3/4/2.54))
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-1, 8, 100000)
        y = np.power(x,2) / np.exp(x)
        plt.plot(x, y, label=r'$y=f(x)$')
        plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

# 题目专用，且单张图片
def single_plot_timu():
        plt.figure(figsize=(6.0/2.54, 6.0*3/4/2.54))
        plt.xticks(fontsize=9)
        plt.yticks(fontsize=9)
        plt.axvline(x=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')
        plt.axhline(y=0, linestyle=':', linewidth=1, c='k', label='_nolegend_')

        x = np.linspace(-np.pi/2, np.pi/2, 10000)
        x1 = np.linspace(-2, 2, 10000)
        x2 = np.linspace(-2, -0.001, 10000)
        y = np.tan(x)
        y[np.abs(y) > 10] = np.nan
        plt.plot(x, y, label=r'$f(x)$')
        plt.axvline(x=-2*np.pi/4, linestyle='--', linewidth=1.5, c='k', label='_nolegend_')
        plt.axvline(x=2*np.pi/4, linestyle='--', linewidth=1.5, c='k', label='_nolegend_')

        #plt.plot(x1, 3*np.power(x1,2), label=r'$f\prime(x)$', linewidth=1.5,c='m')
        #plt.plot(x2, -x1/x1, linewidth=1.5,c='m',label='_nolegend_')

        #plt.plot(x, np.power(x,2)/np.exp(x) - 3.0/4.0, label=r'g(x)', linewidth=1.5)
        plt.xlim(-np.pi/2 -0.2,np.pi/2 +0.2)
        #plt.ylim(-2,2)
        #plt.legend(loc = 0, prop = {'size':9}, handlelength=1)

        plt.xticks([-np.pi/2,0,np.pi/2], [r'$-\frac{\pi}{2}$',r'$0$',r'$\frac{\pi}{2}$'])
        plt.yticks([])

        plt.savefig('./0.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
        plt.show()
        plt.close()

if __name__ == "__main__":
        single_plot_timu()
        #multi_plot_timu_12()
        #single_plot_bold()
        #multi_plot_timu_12()
        #multi_plot_timu_21()
        #multi_plot_timu_22()
        #multi_plot_timu_14()




```





## matplotlib.pyplot



### subplots_adjust
> 子图间距调整

- subplots_adjust(left=0.2)，调整左外边距，默认值为0.125

- subplots_adjust(right=0.1)，调整右外边距，默认值为0.0

- subplots_adjust(top=0.9)，调整上外边距，默认值为0.88

- subplots_adjust(bottom=0.15)，调整下外边距，默认值为0.11

- subplots_adjust(wspace=0.5)，调整子图水平间距，默认值为0.2

- subplots_adjust(hspace=0.4)，调整子图高度间距，默认值为0.2




### title

添加标题

- `plt.title('正角、负角和零角的概念')`


### xlim ylim

调整坐标显示范围

- `plt.xlim(-1.5, 1.5)`
- `plt.ylim(-1.5, 1.5)`









### 指定颜色

{{<note >}}

- https://matplotlib.org/stable/users/explain/colors/colors.html#colors-def


{{</note>}}


##### A. 十进制表示

在 `Matplotlib` 中，绘制不同曲线时，默认的颜色按顺序来自 `color cycle`，这是一个循环的颜色列表。默认情况下，`Matplotlib` 使用以下颜色顺序：

1. 蓝色 (`#1f77b4`)
2. 橙色 (`#ff7f0e`)
3. 绿色 (`#2ca02c`)
4. 红色 (`#d62728`)
5. 紫色 (`#9467bd`)
6. 棕色 (`#8c564b`)
7. 粉色 (`#e377c2`)
8. 灰色 (`#7f7f7f`)
9. 黄色 (`#bcbd22`)
10. 青色 (`#17becf`)

这些颜色的十六进制代码是基于 `Tableau 10` 的调色板，`Matplotlib` 使用该调色板作为默认的颜色循环。

##### B. 单个字母表示颜色

1. 'b' as blue
2. 'g' as green
3. 'r' as red
4. 'c' as cyan
5. 'm' as magenta
6. 'y' as yellow
7. 'k' as black
8. 'w' as white


##### C. rgb表示

- (0.1, 0.2, 0.5)

- (0.1, 0.2, 0.5, 0.3)




### quiver

- https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.quiver.html#matplotlib.pyplot.quiver


#### 箭头示意图

![](/maths_func/20240915-quiver-sizes.svg)

#### 属性名称

| 属性名称       | 功能                                  | 默认值      |
|:---------------|:--------------------------------------|:------------|
| X              | X轴坐标列表                           |             |
| Y              | Y轴坐标列表                           |             |
| U              | 箭头向量的x方向分量列表               |             |
| V              | 箭头向量的y方向分量列表               |             |
| angles         | uv，xy                                 | uv          |
| scale_units    |                                       | None        |
| scale          | 箭头长度缩小比例                      | None        |
| units          |                                       |             |
| pivot          | 确定箭头相对于起点的位置，tail，mid，tip | tail        |
| width          | 箭头线条宽度，以图形宽度为比例         | 0.005，float |
| headwidth      | 箭头宽度，宽一点更好看                 | 3，float     |
| headlength     |                                       | 5，float     |
| headaxislength |                                       | 4.5，float   |
| minshaft       |                                       | 1，float     |
| minlength      |                                       | 1，float     |
| color          | 箭头颜色                              | 黑色        |
| linewidths     | 箭头线宽                              | 1，float     |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |
|                |                                       |             |



- X，Y 定义了箭头的起始位置

```python
X=[1,2,3]
Y=[1,2,3]
```
这会绘制三个箭头，起始点分别是(1,1),(2,2),(3,3),

- U，V 定义了箭头的方向分量

```python
# X方向的分量
U=[1,0,-1]

# Y方向的分量
V=[0,1,-1]
```
这会绘制三个箭头，起始点分别是(1,1),(2,2),(3,3),



- scale_units 箭头缩放单位
  - xy 表示箭头长度与数据坐标一致，不会因为图形的缩放比例而改变
  - width
  - inches 英寸

- angles 决定箭头角度计算方式
  - xy 箭头角度由U，V 定义箭头的方向分量确定，最常用，将箭头方向与坐标系对齐
  - uv 直接使用U，V ，不做任何变换













