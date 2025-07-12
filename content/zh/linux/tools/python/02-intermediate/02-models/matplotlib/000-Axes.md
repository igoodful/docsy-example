---
title: 0. Axes类
description: Axes
weight: 9
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 常见方法
- x = np.array([1,2])
- x = np.linspace(x.min(), x.max(), 100)
- x = np.arange(x.min(), x.max(), 0.01)
- y = np.sin(x)
- plt.subplots(1, 1, figsize=(4,3), dpi=300, constrained_layout=True)
- ax.axvline(x=0, color='k', linewidth=1, ls=':', label='_nolegend_')
- ax.axhline(y=0, color='k', linewidth=1, ls=':', label='_nolegend_')
- ax.plot(x ,y, lw=2, label=r'$y$', color='blue')
- ax.step
- ax.scatter
- ax.set_xlim(0,2) 当需要展示x更多范围
- ax.set_ylim(-1,1) 当需要展示y更多范围
- ax.set_xscale('linear') 默认线性刻度，linear，log，symlog，logit
- ax.set_yscale('linear') 默认线性刻度，linear，log，symlog，logit
- ax.set_xticks() 只有当x轴标签难看，且影响图像的美观，则自定义x轴标签
- ax.set_yticks() 只有当y轴标签难看，且影响图像的美观，则自定义y轴标签
- ax.locator_params
- ax.set_axis_off()
- ax.set_aspect('auto') 默认auto，可设置y/x相等equal
- ax.set_adjustable()
- ax.margins()
- ax.set_xmargin()
- ax.set_ymargin()
- ax.set_title('title') 仅当需要显示子图标题
- ax.set_xlabel(r'$x$') 仅当需要自定义x轴表示指定内容时才需要
- ax.set_ylabel(r'$y$', rotation=360) 仅当需要自定义y轴表示指定内容时才需要
- ax.legend()
- ax.annotate('local max', xy=(2, 1), xytext=(3, 1.5),arrowprops=dict(facecolor='black', shrink=0.05),)
- ax.text(x, y, s, fontsize=12)
- ax.grid(True)
- plt.savefig('./test.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
- plt.show()
- plt.close()
- patches.Rectangle
- patches.CirclePolygon
- patches.Polygon
- patches.Circle
- patches.Ellipse
- patches.Arc
- patches.Wedge

## Axes类所有方法

| 方法                                 | 功能                                           |
|:-------------------------------------|:-----------------------------------------------|
| **1. 绘图与图形生成**                | 开始                                           |
| plot                                 | 绘制线图                                       |
| scatter                              | 绘制散点图                                     |
| bar                                  | 绘制垂直条形图                                 |
| barh                                 | 绘制水平条形图                                 |
| bar_label                            | 给条形图添加标签                               |
| hist                                 | 绘制直方图                                     |
| hist2d                               | 绘制二维直方图                                 |
| pie                                  | 绘制饼图                                       |
| stackplot                            | 绘制堆叠区域图                                 |
| stem                                 | 绘制茎叶图                                     |
| errorbar                             | 绘制误差棒图                                   |
| step                                 | 绘制阶梯图                                     |
| loglog                               | 绘制双对数轴图                                 |
| semilogx                             | 绘制X轴对数的图                                |
| semilogy                             | 绘制Y轴对数的图                                |
| vlines                               | 绘制垂直线                                     |
| hlines                               | 绘制水平线                                     |
| axhline                              | 添加水平线（跨整个轴）                           |
| axvline                              | 添加垂直线（跨整个轴）                           |
| axline                               | 绘制自定义斜线                                 |
| fill_between                         | 在两条线之间填充                               |
| fill_betweenx                        | 在两条垂直线之间填充                           |
| fill                                 | 绘制多边形并填充区域                           |
| axhspan                              | 在指定的Y轴区域之间填充颜色                    |
| axvspan                              | 在指定的X轴区域之间填充颜色                    |
| stairs                               | 绘制阶梯状条形图                               |
| broken_barh                          | 绘制分段的水平条形图                           |
| boxplot                              | 绘制箱线图                                     |
| violinplot                           | 绘制小提琴图                                   |
| bxp                                  | 绘制自定义箱线图                               |
| violin                               | 绘制小提琴图                                   |
| ecdf                                 | 绘制经验分布函数                               |
| contour                              | 绘制等高线                                     |
| contourf                             | 绘制填充等高线                                 |
| pcolor                               | 创建伪彩色图                                   |
| pcolorfast                           | 创建快速伪彩色图                               |
| pcolormesh                           | 绘制网格上的伪彩色图                           |
| imshow                               | 显示图像数据                                   |
| matshow                              | 显示矩阵数据                                   |
| triplot                              | 绘制三角网格线框                               |
| tricontour                           | 绘制三角网格的等高线                           |
| tricontourf                          | 绘制三角网格的填充等高线                       |
| tripcolor                            | 绘制三角网格上的伪彩色图                       |
| quiver                               | 绘制矢量场                                     |
| quiverkey                            | 添加矢量图例                                   |
| barbs                                | 绘制羽毛符号图（气象学）                         |
| streamplot                           | 绘制流线图                                     |
| eventplot                            | 绘制事件发生图                                 |
| acorr                                | 绘制自相关函数                                 |
| xcorr                                | 绘制交叉相关函数                               |
| angle_spectrum                       | 绘制角度频谱                                   |
| cohere                               | 计算两个信号的相干性                           |
| csd                                  | 计算交叉功率谱密度                             |
| magnitude_spectrum                   | 绘制幅值频谱                                   |
| phase_spectrum                       | 绘制相位频谱                                   |
| psd                                  | 绘制功率谱密度                                 |
| specgram                             | 绘制频谱图                                     |
| spy                                  | 绘制稀疏矩阵的图像                             |
| hexbin                               | 绘制六边形柱状图                               |
| **2. 注释与文本操作**                | 开始                                           |
| annotate                             | 添加注释                                       |
| text                                 | 在指定位置添加文本                             |
| table                                | 添加表格                                       |
| arrow                                | 添加箭头                                       |
| clabel                               | 为等高线图添加标签                             |
| **3. 图形调整与布局控制**            | 开始                                           |
| set_xlim                             | 设置X轴范围                                    |
| get_xlim                             | 获取X轴范围                                    |
| set_ylim                             | 设置Y轴范围                                    |
| get_ylim                             | 获取Y轴范围                                    |
| set_xlabel                           | 设置X轴标签                                    |
| get_xlabel                           | 获取X轴标签                                    |
| set_ylabel                           | 设置Y轴标签                                    |
| get_ylabel                           | 获取Y轴标签                                    |
| set_title                            | 设置图形标题                                   |
| get_title                            | 获取图形标题                                   |
| legend                               | 显示图例                                       |
| get_legend                           | 获取图例                                       |
| get_legend_handles_labels            | 获取图例句柄和标签                             |
| set_position                         | 设置轴的位置                                   |
| get_position                         | 获取轴的位置                                   |
| reset_position                       | 重置轴的位置                                   |
| set_box_aspect                       | 设置盒子的纵横比                               |
| get_box_aspect                       | 获取盒子的纵横比                               |
| set_aspect                           | 设置坐标轴的比例                               |
| get_aspect                           | 获取坐标轴的比例                               |
| set_adjustable                       | 设置调整方式                                   |
| get_adjustable                       | 获取调整方式                                   |
| apply_aspect                         | 应用比例设置                                   |
| set_anchor                           | 设置轴的锚点                                   |
| get_anchor                           | 获取轴的锚点                                   |
| axis                                 | 设置坐标轴范围和可见性                         |
| margins                              | 设置坐标轴边距                                 |
| zorder                               | 设置元素的绘制顺序                             |
| set_axisbelow                        | 设置轴线是否在图形元素下方                     |
| get_axisbelow                        | 获取轴线是否在图形元素下方                     |
| update_datalim                       | 更新数据边界                                   |
| autoscale_view                       | 调整视图以适应数据                             |
| set_autoscale_on                     | 启用/禁用自动缩放                              |
| get_autoscale_on                     | 获取是否启用自动缩放                           |
| set_autoscalex_on                    | 启用/禁用X轴自动缩放                           |
| get_autoscalex_on                    | 获取X轴自动缩放状态                            |
| set_autoscaley_on                    | 启用/禁用Y轴自动缩放                           |
| get_autoscaley_on                    | 获取Y轴自动缩放状态                            |
| set_xbound                           | 设置X轴的上下界限                              |
| get_xbound                           | 获取X轴的上下界限                              |
| set_ybound                           | 设置Y轴的上下界限                              |
| get_ybound                           | 获取Y轴的上下界限                              |
| get_xaxis_transform                  | 获取X轴变换矩阵                                |
| get_yaxis_transform                  | 获取Y轴变换矩阵                                |
| get_data_ratio                       | 获取数据宽高比                                 |
| get_xaxis_text1_transform            | 获取X轴标签的第一个变换                        |
| get_xaxis_text2_transform            | 获取X轴标签的第二个变换                        |
| get_yaxis_text1_transform            | 获取Y轴标签的第一个变换                        |
| get_yaxis_text2_transform            | 获取Y轴标签的第二个变换                        |
| set_xbound                           | 设置X轴的边界                                  |
| get_xbound                           | 获取X轴的边界                                  |
| set_ybound                           | 设置Y轴的边界                                  |
| get_ybound                           | 获取Y轴的边界                                  |
| use_sticky_edges                     | 控制是否使用粘性边界（确保图形不会超出轴的边界） |
| **4. 坐标轴与刻度设置**              | 开始                                           |
| set_xticks                           | 设置X轴刻度                                    |
| get_xticks                           | 获取X轴刻度                                    |
| set_xticklabels                      | 设置X轴刻度标签                                |
| get_xticklabels                      | 获取X轴刻度标签                                |
| get_xmajorticklabels                 | 获取X轴主刻度标签                              |
| get_xminorticklabels                 | 获取X轴次刻度标签                              |
| set_yticks                           | 设置Y轴刻度                                    |
| get_yticks                           | 获取Y轴刻度                                    |
| set_yticklabels                      | 设置Y轴刻度标签                                |
| get_yticklabels                      | 获取Y轴刻度标签                                |
| get_ymajorticklabels                 | 获取Y轴主刻度标签                              |
| get_yminorticklabels                 | 获取Y轴次刻度标签                              |
| xaxis_date                           | 设置X轴日期格式                                |
| yaxis_date                           | 设置Y轴日期格式                                |
| tick_params                          | 设置刻度线的显示                               |
| locator_params                       | 设置刻度定位参数                               |
| ticklabel_format                     | 设置刻度标签的显示格式                         |
| set_xscale                           | 设置X轴比例                                    |
| get_xscale                           | 获取X轴比例                                    |
| set_yscale                           | 设置Y轴比例                                    |
| get_yscale                           | 获取Y轴比例                                    |
| set_xmargin                          | 设置X轴边距                                    |
| get_xmargin                          | 获取X轴边距                                    |
| set_ymargin                          | 设置Y轴边距                                    |
| get_ymargin                          | 获取Y轴边距                                    |
| autoscale                            | 自动缩放轴                                     |
| relim                                | 重新计算数据边界                               |
| get_xgridlines                       | 获取X轴的网格线                                |
| get_xticklines                       | 获取X轴的刻度线                                |
| get_ygridlines                       | 获取Y轴的网格线                                |
| get_yticklines                       | 获取Y轴的刻度线                                |
| minorticks_off                       | 关闭次刻度线                                   |
| minorticks_on                        | 启用次刻度线                                   |
| convert_xunits                       | 将X轴数据转换为单位                            |
| convert_yunits                       | 将Y轴数据转换为单位                            |
| have_units                           | 检查坐标轴是否有单位                           |
| secondary_xaxis                      | 添加次级X轴                                    |
| secondary_yaxis                      | 添加次级Y轴                                    |
| inset_axes                           | 在主图中添加内嵌的子图                         |
| indicate_inset                       | 标识内嵌子图                                   |
| indicate_inset_zoom                  | 标识并放大内嵌子图                             |
| get_shared_x_axes                    | 获取共享X轴的对象                              |
| get_shared_y_axes                    | 获取共享Y轴的对象                              |
| twinx                                | 创建与当前X轴共享的Y轴                         |
| twiny                                | 创建与当前Y轴共享的X轴                         |
| sharex                               | 设置共享的X轴                                  |
| sharey                               | 设置共享的Y轴                                  |
| label_outer                          | 设置外部标签，隐藏与其他共享轴的重叠标签        |
| get_xaxis                            | 获取X轴对象                                    |
| get_yaxis                            | 获取Y轴对象                                    |
| **5. 图形内容与状态管理**            | 开始                                           |
| add_line                             | 添加线条                                       |
| add_image                            | 添加图像                                       |
| add_patch                            | 添加图形补丁                                   |
| add_table                            | 添加表格                                       |
| cla                                  | 清除轴                                         |
| clear                                | 清除图形内容                                   |
| grid                                 | 控制网格线显示                                 |
| invert_xaxis                         | 反转X轴                                        |
| invert_yaxis                         | 反转Y轴                                        |
| xaxis_inverted                       | 检查X轴是否反转                                |
| yaxis_inverted                       | 检查Y轴是否反转                                |
| get_children                         | 获取所有子对象                                 |
| get_lines                            | 获取图形中的线条                               |
| get_images                           | 获取图形中的图像                               |
| draw                                 | 重绘图形                                       |
| draw_artist                          | 重绘指定对象                                   |
| findobj                              | 查找对象                                       |
| set_axis_off                         | 关闭坐标轴                                     |
| set_axis_on                          | 打开坐标轴                                     |
| **6. 用户交互与导航**                |                                                |
| can_pan                              | 检查是否支持平移                               |
| can_zoom                             | 检查是否支持缩放                               |
| start_pan                            | 开始平移操作                                   |
| end_pan                              | 结束平移操作                                   |
| drag_pan                             | 拖动平移                                       |
| mouseover                            | 鼠标悬停事件                                   |
| in_axes                              | 检查点是否在轴内                               |
| contains                             | 检查是否包含某点                               |
| contains_point                       | 检查具体点是否在对象内                         |
| set_navigate                         | 设置是否允许导航                               |
| get_navigate                         | 检查是否允许导航                               |
| format_coord                         | 格式化坐标显示                                 |
| get_navigate_mode                    | 获取当前导航模式                               |
| set_navigate_mode                    | 设置导航模式                                   |
| get_forward_navigation_events        | 获取前进导航事件                               |
| set_forward_navigation_events        | 设置前进导航事件                               |
| format_cursor_data                   | 格式化光标数据                                 |
| format_xdata                         | 格式化X数据                                    |
| format_ydata                         | 格式化Y数据                                    |
| add_artist                           | 添加艺术家对象                                 |
| add_child_axes                       | 添加子轴                                       |
| add_collection                       | 添加集合对象                                   |
| add_container                        | 添加容器对象                                   |
| stale                                | 检查图形是否已过时                             |
| pchanged                             | 检查图形状态是否变化                           |
| add_callback                         | 添加回调函数                                   |
| remove_callback                      | 移除回调函数                                   |
| get_cursor_data                      | 获取光标数据                                   |
| redraw_in_frame                      | 在当前帧内重绘                                 |
| get_rasterization_zorder             | 获取光栅化顺序                                 |
| set_rasterization_zorder             | 设置光栅化顺序                                 |
| get_window_extent                    | 获取窗口边界                                   |
| get_tightbbox                        | 获取紧凑的边界框                               |
| name                                 | 获取图形的名称                                 |
| get_default_bbox_extra_artists       | 获取默认的边界框附加艺术家                     |
| get_transformed_clip_path_and_affine | 获取转换的剪切路径和仿射变换                   |
| has_data                             | 检查图形是否有数据                             |
| **7. 杂类**                          | 开始                                           |
| set_facecolor                        | 设置背景颜色                                   |
| get_facecolor                        | 获取背景颜色                                   |
| set_prop_cycle                       | 设置属性循环                                   |
| get_prop_cycle                       | 获取属性循环                                   |
| set_frame_on                         | 显示或隐藏框架                                 |
| get_frame_on                         | 获取框架是否可见                               |
| set_axes_locator                     | 设置轴定位器                                   |
| get_axes_locator                     | 获取轴定位器                                   |
| set_subplotspec                      | 设置子图规格                                   |
| get_subplotspec                      | 获取子图规格                                   |
| set                                  | 设置多个属性（简便方法，用于同时设置多个参数）    |
