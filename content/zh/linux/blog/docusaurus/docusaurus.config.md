---
title: docusaurus.config
description: docusaurus
date: 2017-01-05
weight: 2
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< note >}}

{{< /note >}}



```bash

npx create-docusaurus@latest my-website classic --typescript

# docusaurus 3 安装 remark-math@6 rehype-katex@7
npm install --save remark-math@6 rehype-katex@7

npm install --save-dev typescript @docusaurus/module-type-aliases @docusaurus/tsconfig @docusaurus/types

# npm run start后面必需接--
npm run start -- --host 0.0.0.0 --port 3000 --config ./docusaurus.config.js

npm run start --  --host 0.0.0.0 --port 4000



npm run build

```




## module.exports



| 选项                  | 值                           | 说明               |
|:----------------------|:-----------------------------|:-------------------|
| title                 | 标题                         |                    |
| tagline               | 副标题                       |                    |
| organizationName      | 组织名称                     |                    |
| projectName           | 项目名称                     |                    |
| url                   |                              |                    |
| baseUrl               |                              |                    |
| favicon               | 图标                         |                    |
| onBrokenLinks         | throw，warn                   | 建议throw          |
| onBrokenMarkdownLinks | throw，warn                   | 建议warn           |
| i18n                  | defaultLocale，locales数组    | 对象，最佳为zh-Hans |
| themeConfig           |                              | 对象               |
| presets               | `@docusaurus/preset-classic` | 数组类型           |
| plugins               |                              |                    |
| customFields          |                              |                    |
| themes                |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |
|                       |                              |                    |



## plugins

一个插件由插件名称和选项对象组成，如果要声明选项，需要在配置里把名称和配置对象放在一个二元组中。 这种风格通常被称作 Babel 风格。

```yaml
export default {
  plugins: [
    // 基础用法
    '@docusaurus/plugin-debug',

    // 包含选项对象（Babel 风格）
    [
      '@docusaurus/plugin-sitemap',
      {
        changefreq: 'weekly',
      },
    ],
  ],
};

```

| plugins                  |   | 数组 |
|:-------------------------|:--|:-----|
| `docusaurus-plugin-sass` |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |
|                          |   |      |



### @docusaurus/plugin-content-docs

所有的 Docusaurus 内容插件都可支持多个插件实例。 比如，可以有多个文档插件实例或多个博客。 每个插件实例都需要分配一个唯一的 ID。插件 ID 默认为 default。




### docusaurus-plugin-sass sass

#### 1. 安装
```bash
npm install @ionic-internal/ionic-ds docusaurus-plugin-sass sass


```

#### 2. 配置
```yaml
plugins: [
    "docusaurus-plugin-sass",
  ],
```

#### 3. 使用
```scss
@import '~@ionic-internal/ionic-ds/dist/tokens/tokens.css';

// 你可以在此处编写自定义样式，或者重写 Ionic 的样式
body {
  font-family: var(--ion-font-family, 'Helvetica', 'Arial', sans-serif);
  background-color: var(--ion-background-color, #ffffff);
}

.navbar__title {
  color: var(--ion-color-primary, #3880ff);
}

.footer {
  background-color: var(--ion-background-color-dark, #222428);
  color: var(--ion-text-color, #ffffff);
}
```

#### 4. 首页
如果你希望自定义首页布局，可以在 `src/pages/index.js` 文件中添加自定义组件和布局。例如：

```javascript
import React from 'react';
import Layout from '@theme/Layout';
import styles from './index.module.css';

export default function Home() {
  return (
    <Layout
      title={`Welcome to Ionic Docs`}
      description="A Docusaurus site mimicking Ionic Docs layout">
      <main className={styles.main}>
        <h1 className={styles.title}>Welcome to Ionic Docs</h1>
        <p className={styles.description}>
          This is a custom Docusaurus site with Ionic-like styles and layout.
        </p >
      </main>
    </Layout>
  );
}
```




## presets



|   |   |   |
|:--|:--|:--|
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |



## themeConfig



| themeConfig     |                                         |          |
|:----------------|:----------------------------------------|:---------|
| announcementBar | id,content,isCloseable                  | 对象     |
| metadata        | name,content                            | 对象数组 |
| colorMode       | defaultMode                             | 对象     |
| prism           | theme对象,additionalLanguages数组       | 对象     |
| algolia         | appId,apiKey,indexName,contextualSearch | 对象     |
| footer          |                                         | 对象     |
| navbar          |                                         | 对象     |
|                 |                                         |          |
|                 |                                         |          |
|                 |                                         |          |
|                 |                                         |          |
|                 |                                         |          |
|                 |                                         |          |
































