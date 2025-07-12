---
title: 字体
date: 2023-10-01
weight: 2
description: >
  使用本地的airbnb字体.
---

# 配置：hugo.toml

```toml
# 1. 新增_variables_project.scss文件：your_project\assets\scss\_variables_project.scss
# 新增内容如下：
$font-family-serif: 'YourCustomFont', serif;
$font-family-sans-serif: 'YourCustomFont', sans-serif;
@font-face {
        font-family: 'YourCustomFont';
        src: url('/fonts/AirbnbCereal-Light.ttf') format('truetype');
        font-weight: normal;
        font-style: normal;
}

# 2. 新增字体AirbnbCereal-Light.ttf文件：your_project\static\fonts\AirbnbCereal-Light.ttf

```

