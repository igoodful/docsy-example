---
title: 安装docsy主题
date: 2023-10-01
weight: 1
description: 人靠衣装，美靠靓装.
---


> 主题图片配置：your_project/content/zh/featured-background.jpg

## hugo.yaml

```yaml
theme: "docsy"
```

## 下载如下三个项目到对应目录

谷歌的hugo主题docsy：`https://github.com/google/docsy/archive/refs/tags/v0.10.0.tar.gz`
- /project/themes/docsy

字体下载：`https://github.com/FortAwesome/Font-Awesome/archive/refs/tags/6.6.0.tar.gz`
- /project/themes/github.com/FortAwesome/Font-Awesome

bootstrap下载：`https://github.com/twbs/bootstrap/archive/refs/tags/v5.3.3.tar.gz`
- /project/themes/github.com/twbs/bootstrap


## 运行

```bash
hugo server --bind 0.0.0.0
```




