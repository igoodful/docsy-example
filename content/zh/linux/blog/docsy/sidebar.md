---
title: 修改左侧导航栏只能显示50篇文档标题
description: "docsy/layouts/partials/sidebar-tree.html"
date: 2024-06-10
weight: 2000
viz: true
---


文件：`docsy/layouts/partials/sidebar-tree.html`

修改内容：`{{ $sidebarMenuTruncate := .Site.Params.ui.sidebar_menu_truncate | default 50 -}}`改成`{{ $sidebarMenuTruncate := .Site.Params.ui.sidebar_menu_truncate | default 5000 -}}`







