---
title: install
date: 2023-10-01
weight: 3
description: >
  install.
---

# 配置：hugo.toml

```bash
#!/bin/bash

node -v
nvm install --lts
npm install --save-dev autoprefixer postcss-cli postcss

hugo new site myproject
cd myproject
git init

git submodule add https://github.com/google/docsy.git themes/docsy
cd themes/docsy
git checkout v0.6.0

echo 'theme = "docsy"' >> config.toml

(cd themes/docsy && npm install)

hugo serve
```

