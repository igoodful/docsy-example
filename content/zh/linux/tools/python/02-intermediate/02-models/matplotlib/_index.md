---
title: Matplotlib
description: Matplotlib
weight: 200
---















## 20240928


```python
#!/usr/bin/env python
#!encoding=utf-8
import numpy as np
import matplotlib.pyplot as plt
import matplotlib as mpl
plt.rcParams['font.family'] = 'Microsoft YaHei'
plt.rcParams['figure.titlesize'] = 12
plt.rcParams['figure.labelsize'] = 9
# 自动调节子图
plt.rcParams['figure.autolayout'] = True
plt.rcParams["figure.dpi"] = 150
plt.rcParams["savefig.dpi"] = 300
plt.rcParams["savefig.facecolor"] = 'white'
plt.rcParams["savefig.edgecolor"] = 'black'
plt.rcParams["savefig.pad_inches"] = 0.05
plt.rcParams["axes.titlecolor"] = 'k'
plt.rcParams["axes.titlesize"] = 10
plt.rcParams["legend.title_fontsize"] = 9
plt.rcParams["legend.fontsize"] = 8
plt.rcParams["xtick.labelsize"] = 9
plt.rcParams["ytick.labelsize"] = 9
plt.rcParams['font.size'] = 9
plt.rcParams['axes.labelsize'] = 9
plt.rcParams["lines.linewidth"] = 1
plt.rcParams["xtick.color"] = 'k'
plt.rcParams["text.color"] = 'k'
#plt.rcParams["text.usetex"] = True

def one_theory():
        fig,ax = plt.subplots(figsize=(8.0/2.54, 8.0*3/4/2.54), constrained_layout=True)
        # 虚线坐标轴
        ax.axvline(x=0, c='k', ls=':', label='_nolegend_')
        ax.axhline(y=0, c='k', ls=':', label='_nolegend_')
        # ax.scatter(0, 0, s=4)
        

        x = np.linspace(0, 4, 10000)
        y = x**2 / np.log(x)
        # y[np.abs(y) > 20] = np.nan
        ax.plot(x,y,lw=1.5,label=r'$x^2+ 2x -5$')

        # 渐近线
        ax.axvline(x=1, c='k', ls='--', label='_nolegend_')

        # fig.subplots_adjust(bottom=0.2, left=0.2)

        # 设置显示轴范围
        XMIN=-2
        XMAX=2
        YMIN=1
        YMAX=10
        # ax.set_xlim(XMIN, XMAX)
        # ax.set_ylim(YMIN, YMAX)
        # 适合画圆和椭圆等对称图像,均等缩放
        # ax.set_aspect('equal', adjustable='box')

        # 添加备注和箭头文本
        # ax.text(0, 0, r'\n')
        # ax.text(0, 0, r'$x^2$', horizontalalignment='center', verticalalignment='center', transform = ax.transAxes, fontsize=12, color='green')
        # ax.annotate('', xy=(ax.get_xlim()[1],0), xytext=(ax.get_xlim()[0],0),arrowprops=dict(arrowstyle='->'),annotation_clip=False)
        # ax.annotate('', xy=(0,ax.get_ylim()[1]), xytext=(0,ax.get_ylim()[0]),arrowprops=dict(arrowstyle='->'),annotation_clip=False)
        # ax.annotate(xy=(0,0),xytext=(1,1),text=r'$\frac{sin(x)^2}{x}$',fontsize=8,arrowprops={},bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

        # 自定义刻度和刻度标签
        # XTICKS=np.arange(-2, 3, 1)
        # YTICKS=np.arange(-2, 10, 2)
        # ax.set_xticks(XTICKS)
        # ax.set_yticks(YTICKS)
        # ax.set_xticklabels(XTICKS)
        # ax.set_yticklabels(YTICKS)
        # ax.set_xticklabels([])
        # ax.set_yticklabels([])
        # 可减少或增加刻度密度
        ax.locator_params('x', tight=True, nbins=4)
        ax.locator_params('y', tight=True, nbins=2)

        # 网格和图例显示
        # ax.grid(True)
        # ax.legend(handlelength=1)
        
        # 标题和轴标题
        # ax.set_title("title")
        # ax.set_xlabel(r'$x$ ')
        # ax.set_ylabel(r'$y$')
        ########################################
        # ax.set_title('Scatter', fontsize=16, fontweight='bold', color='blue', backgroundcolor='blue', pad = 20, ha='right', va='bottom', loc='left')
        # ax.set_xlabel(r'$\frac{\sin x}{x}$ ', fontsize=20, rotation=90, color='blue, fontweight='bold')

        plt.savefig('./one_timu.svg', bbox_inches='tight')
        plt.show()
        plt.close()

def one_timu():
        fig,ax = plt.subplots(figsize=(6.0/2.54, 6.0*3/4/2.54), constrained_layout=True)
        # 虚线坐标轴
        ax.axvline(x=0, c='k', ls=':', label='_nolegend_')
        ax.axhline(y=0, c='k', ls=':', label='_nolegend_')
        # ax.scatter(0, 0, s=4)
        

        x = np.linspace(0, 4, 10000)
        y = x**2 / np.log(x)
        y[np.abs(y) > 20] = np.nan
        ax.plot(x,y,lw=1.5,label=r'$x^2+ 2x -5$')

        # 渐近线
        ax.axvline(x=1, c='k', ls='--', label='_nolegend_')
        # fig.subplots_adjust(bottom=0.2, left=0.2)

        # 设置显示轴范围
        XMIN=0
        XMAX=20
        YMIN=1
        YMAX=10
        # ax.set_xlim(XMIN, XMAX)
        # ax.set_ylim(YMIN, YMAX)
        # 适合画圆和椭圆等对称图像,均等缩放
        # ax.set_aspect('equal', adjustable='box')

        # 添加备注和箭头文本
        # ax.text(0, 0, r'\n')
        # ax.text(0, 0, r'$x^2$', horizontalalignment='center', verticalalignment='center', transform = ax.transAxes, fontsize=12, color='green')
        # ax.annotate('', xy=(ax.get_xlim()[1],0), xytext=(ax.get_xlim()[0],0),arrowprops=dict(arrowstyle='->'),annotation_clip=False)
        # ax.annotate('', xy=(0,ax.get_ylim()[1]), xytext=(0,ax.get_ylim()[0]),arrowprops=dict(arrowstyle='->'),annotation_clip=False)
        # ax.annotate(xy=(0,0),xytext=(1,1),text=r'$\frac{sin(x)^2}{x}$',fontsize=8,arrowprops={},bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

        # 自定义刻度和刻度标签
        # XTICKS=np.arange(-2, 3, 1)
        # YTICKS=np.arange(-2, 10, 2)
        # ax.set_xticks(XTICKS)
        # ax.set_yticks(YTICKS)
        # ax.set_xticklabels(XTICKS)
        # ax.set_yticklabels(YTICKS)
        # ax.set_xticklabels([])
        # ax.set_yticklabels([])
        # 可减少或增加刻度密度
        # ax.locator_params('x', tight=True, nbins=8)
        # ax.locator_params('y', tight=True, nbins=4)

        # 网格和图例显示
        # ax.grid(True)
        # ax.legend(handlelength=1)
        
        # 标题和轴标题
        # ax.set_title("title")
        # ax.set_xlabel(r'$x$ ')
        # ax.set_ylabel(r'$y$')
        ########################################
        # ax.set_title('Scatter', fontsize=16, fontweight='bold', color='blue', backgroundcolor='blue', pad = 20, ha='right', va='bottom', loc='left')
        # ax.set_xlabel(r'$\frac{\sin x}{x}$ ', fontsize=20, rotation=90, color='blue, fontweight='bold')

        plt.savefig('./one_timu.svg', bbox_inches='tight')
        plt.show()
        plt.close()



if __name__ == "__main__":
        one_timu()



```











