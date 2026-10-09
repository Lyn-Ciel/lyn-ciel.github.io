# 我的个人网站

基于 [Astro](https://astro.build) 的静态站点：**技术博客 + 项目作品集**。Markdown 写内容，构建成纯静态文件，免费托管到 GitHub Pages。

## 本地开发

```bash
pnpm install      # 首次安装依赖
pnpm dev          # 启动本地预览（浏览器打开终端提示的地址）
pnpm build        # 构建到 dist/
pnpm preview      # 本地预览构建结果
```

> **前提**：电脑要装 Node.js（https://nodejs.org 选 LTS 版）。装好后用 `npm i -g pnpm` 装 pnpm，或直接用 `npm install` + `npm run dev`。
> 以后若新增带原生脚本的依赖、首次安装提示 `Ignored build scripts`，运行一次 `pnpm approve-builds --all` 即可。

## 怎么发一篇新文章

在 `src/content/blog/` 下新建一个 `.md` 文件，顶部写这些信息即可：

```md
---
title: "文章标题"
description: "一句话摘要"
pubDate: 2026-10-09
tags: ["STM32", "嵌入式"]
---

正文写在这里，Markdown 语法。
```

## 怎么加一个新项目

在 `src/content/projects/` 下新建 `.md` 文件：

```md
---
title: "项目名"
description: "一句话介绍"
tech: ["STM32", "C"]
status: "进行中"
---

项目详情。
```

## 怎么加图片 / 文件

把图片或文件丢进 `public/` 文件夹，它们会被**原样发布**到网站上：

```
public/
  images/    ← 放图片（jpg / png / webp / gif / svg）
  files/     ← 放 PDF、压缩包、报告等任意文件
```

**在文章里显示图片**（路径以 `/` 开头，对应 `public/` 根目录）：

```md
![图片描述](/images/你的图片.jpg)
```

**生成一个可下载的文件链接**：

```md
[下载原理图 PDF](/files/原理图.pdf)
```

> 提示：
> - `public/images/a.jpg` → 网址路径是 `/images/a.jpg`；
> - 图片建议先压缩（可用 tinypng 等），GitHub 单文件上限 100MB，图片太大会让网页变慢；
> - 图片引用的是网址路径，所以放在文章任意位置都能显示。

## 部署到 GitHub Pages（免费）

1. 把整个 `website` 目录推到 GitHub 仓库（例如 `my-site`）；
2. 在 GitHub 仓库 → Settings → Pages → Source 选 `GitHub Actions`；
3. 在项目里加一个 `.github/workflows/deploy.yml`（内容见下）；
4. 修改 `astro.config.mjs`：
   - 如果是**项目页**（地址形如 `username.github.io/my-site/`），把 `base` 改成 `/my-site/`；
   - 如果是**用户主页**（`username.github.io`），`base` 保持 `/`。
5. 把 `site` 改成你的真实地址。

`.github/workflows/deploy.yml` 参考：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: withastro/action@v2
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/deploy-pages@v4
```

## 目录结构

```
src/
  content/blog/        # 博客文章（Markdown）
  content/projects/    # 项目（Markdown）
  pages/               # 页面
  components/          # 可复用组件
  layouts/             # 布局
  styles/global.css    # 样式
```
