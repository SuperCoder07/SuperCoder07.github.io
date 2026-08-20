---
title: Hexo + Butterfly + GitHub Pages 搭建全流程
date: 2026-08-20 18:00:00
author: winner
categories:
  - 技术笔记
tags:
  - Hexo
  - Butterfly
  - GitHub Pages
  - GitHub Actions
  - 博客搭建
description: 从零开始用 Hexo 静态站点生成器搭配 Butterfly 主题，通过 GitHub Pages 免费部署个人博客的完整流程，含工具说明与踩坑总结
cover: https://picsum.photos/800/450?random=6
---

## 前言

本文记录了从零搭建一个免费个人博客的完整过程。技术栈选择的是 Hexo（静态站点生成器）+ Butterfly（主题）+ GitHub Pages（托管）+ GitHub Actions（自动部署），全程零成本，只需要一个 GitHub 账号和一台能联网的电脑。

最终效果：访问 `https://SuperCoder07.github.io` 即可看到博客，支持中文界面、文章分类标签、本地搜索、评论系统、暗黑模式自动切换、在线发布等功能。

## 整体架构

在动手之前，先理清各组件之间的关系：

| 组件 | 角色 | 说明 |
|------|------|------|
| Node.js + npm | 运行环境 | Hexo 基于 Node.js，npm 负责安装依赖 |
| Hexo | 静态站点生成器 | 把 Markdown 文章编译成 HTML 静态页面 |
| Butterfly | 主题 | 控制博客的外观、布局、交互 |
| Git | 版本控制 | 管理博客源码，推送到 GitHub |
| GitHub Pages | 托管平台 | 免费提供静态网站访问 |
| GitHub Actions | CI/CD | 推送代码后自动构建并部署 |
| Giscus | 评论系统 | 基于 GitHub Discussions 的轻量评论 |

数据流向很简单：本地写 Markdown -> Hexo 编译成 HTML -> Git 推送到 GitHub -> Actions 自动构建 -> Pages 上线。

## 环境准备

### 1. 安装 Node.js

前往 [Node.js 官网](https://nodejs.org/) 下载 LTS 版本，安装后在终端验证：

```bash
node -v
npm -v
```

两个命令都能输出版本号即可。npm 会随 Node.js 一起安装。

### 2. 安装 Git

前往 [Git 官网](https://git-scm.com/) 下载安装。安装后验证：

```bash
git --version
```

### 3. 安装 Hexo 脚手架

Hexo 提供了命令行工具 `hexo-cli`，通过 npm 全局安装：

```bash
npm install -g hexo-cli
```

安装完成后，`hexo` 命令就可以在任意目录使用了。

## 初始化博客

### 1. 创建博客项目

选一个目录，执行初始化命令：

```bash
hexo init blog
cd blog
npm install
```

`hexo init` 会生成一套标准的 Hexo 项目结构：

| 目录/文件 | 说明 |
|-----------|------|
| `source/_posts/` | 文章存放目录，Markdown 文件放在这里 |
| `source/_data/` | 自定义数据文件 |
| `themes/` | 主题目录 |
| `_config.yml` | 站点配置文件 |
| `package.json` | 依赖管理 |
| `scaffolds/` | 文章模板 |

### 2. 本地预览

```bash
hexo server
```

默认在 `http://localhost:4000` 启动本地服务器，打开浏览器即可预览。此时用的是默认主题 Landscape，下一步替换为 Butterfly。

## 安装 Butterfly 主题

### 1. 安装主题

Hexo 5 以上版本推荐通过 npm 安装主题，也可以直接 git clone 到 `themes/` 目录。这里采用 git clone 方式：

```bash
git clone https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

然后在 `_config.yml` 中设置主题：

```yaml
theme: butterfly
```

### 2. 安装主题依赖的渲染器

Butterfly 使用 Pug 模板引擎和 Stylus 样式预处理器，需要安装对应的渲染插件：

```bash
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

### 3. 创建主题配置文件

Hexo 主题有自己的配置文件 `_config.butterfly.yml`，放在项目根目录，用于覆盖主题默认配置。从 `themes/butterfly/_config.yml` 复制一份即可：

```bash
copy themes\butterfly\_config.yml _config.butterfly.yml
```

所有主题定制都改这个文件，不动 `themes/butterfly/_config.yml` 原文件。

## 站点配置

打开 `_config.yml`，修改站点基本信息：

```yaml
# Site
title: winner
subtitle: 记录技术与生活
description: winner 的个人技术博客
author: winner
language: zh-CN
timezone: Asia/Shanghai

# URL
url: https://your-username.github.io
```

`url` 要填你最终的 GitHub Pages 地址，格式为 `https://用户名.github.io`。

## 主题美化

在 `_config.butterfly.yml` 中做以下定制。

### 1. 导航菜单（中文化）

```yaml
menu:
  首页: / || fas fa-home
  归档: /archives/ || fas fa-archive
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  友链: /link/ || fas fa-link
  关于: /about/ || fas fa-heart
  发布: /publish/ || fas fa-pen-to-square
```

每项格式为 `名称: 路径 || 图标`，图标使用 FontAwesome 类名。固定导航栏设置：

```yaml
nav:
  fixed: true
```

### 2. 代码块美化

```yaml
code_blocks:
  theme: light
  macStyle: true      # Mac 风格圆角窗口
  copy: true           # 一键复制按钮
  language: true       # 显示语言标签
  shrink: false        # 默认展开
```

### 3. 头像设置

把自定义头像图片放到 `themes/butterfly/source/img/` 目录下，然后配置：

```yaml
avatar:
  img: /img/avatar.jpg
  effect: true   # 头像旋转动效

favicon: /img/avatar.jpg   # 浏览器标签页图标
```

### 4. 封面图

```yaml
cover:
  index_enable: true
  aside_enable: true
  archives_enable: true
  default_cover:
    - https://picsum.photos/800/450?random=1
    - https://picsum.photos/800/450?random=2
```

文章没有单独指定封面时，会从 `default_cover` 列表中随机选一张。

### 5. 暗黑模式

```yaml
darkmode:
  enable: true
  button: true        # 显示切换按钮
  autoChangeMode: 1   # 跟随系统设置自动切换
```

### 6. 本地搜索

先安装搜索插件：

```bash
npm install hexo-generator-search --save
```

然后在主题配置中启用：

```yaml
search:
  use: local_search
```

### 7. 评论系统 Giscus

Giscus 是一个基于 GitHub Discussions 的评论系统，无需后端服务器。前往 [giscus.app](https://giscus.app/) 配置仓库后，把生成的参数填入：

```yaml
comments:
  use: Giscus

giscus:
  repo: your-username/your-username.github.io
  repo_id: XXXX
  category_id:XXXXX
  light_theme: light
  dark_theme: dark
```

前提是仓库已开启 Discussions 功能，且安装了 Giscus App。

## 创建页面

Hexo 的独立页面通过 `hexo new page` 创建：

```bash
hexo new page tags
hexo new page categories
hexo new page link
hexo new page about
hexo new page publish
```

每个页面会在 `source/` 下生成对应目录和 `index.md`。编辑各 `index.md` 的 front-matter 指定页面类型，例如标签页：

```yaml
---
title: 标签
type: tags
---
```

## GitHub 仓库准备

### 1. 创建仓库

在 GitHub 上新建仓库，名称必须为 `用户名.github.io`（例如 `your-username.github.io`），这样才能启用用户级 Pages。仓库设为 Public。

### 2. 配置 Git 身份

```bash
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

### 3. 关联远程仓库

在博客项目目录初始化 Git 并关联远程地址：

```bash
git init
git remote add origin https://github.com/your-username/your-username7.github.io.git
```

## 自动部署配置

### 1. GitHub Actions 工作流

在项目根目录创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Hexo

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 18
      - run: rm -f package-lock.json && npm install
      - run: npx hexo generate
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

工作流分两个 Job：`build` 负责 checkout 代码、安装依赖、生成静态文件并打包上传；`deploy` 负责把构建产物发布到 GitHub Pages。

### 2. 修改 Pages 部署来源

进入 GitHub 仓库的 Settings -> Pages -> Build and deployment，把 Source 从默认的 "Deploy from a branch" 改为 **"GitHub Actions"**。这一步很关键，否则 Actions 部署不会生效。

### 3. 推送代码触发部署

```bash
git add .
git commit -m "init blog"
git branch -M main
git push -u origin main
```

推送后进入仓库的 Actions 页面，可以看到 `Deploy Hexo` 工作流开始运行。构建完成（绿色对勾）后，访问 `https://SuperCoder07.github.io` 即可看到博客。

## 在线发布功能

由于 Hexo 是纯静态博客，没有后台管理系统，发文章通常需要在本地写 Markdown 然后 push。为了让用户能在网页上直接发文章，利用了 GitHub 的在线文件编辑功能：

1. 在导航栏添加 `发布` 页面
2. 页面中放置一个按钮，链接指向 GitHub 的新建文件页面：
   `https://github.com/SuperCoder07/SuperCoder07.github.io/new/main/source/_posts`
3. 用户点击后在 GitHub 网页编辑器中填写文件名（如 `source/_posts/新文章.md`），粘贴文章模板并提交
4. 提交触发 Actions 自动构建部署，文章上线

文章模板：

```yaml
---
title: 文章标题
date: 2026-08-20 16:00:00
author: winner
categories:
  - 技术笔记
tags:
  - 标签1
  - 标签2
description: 文章摘要
cover: https://picsum.photos/800/450?random=1
---

正文内容，使用 Markdown 语法。
```

## 常用命令速查

| 命令 | 作用 |
|------|------|
| `hexo new "标题"` | 新建文章 |
| `hexo new page 名称` | 新建页面 |
| `hexo server` | 本地预览（端口 4000） |
| `hexo clean` | 清理生成的文件 |
| `hexo generate` | 生成静态文件 |
| `hexo deploy` | 部署（配合 deploy 插件） |
| `npm install 包名 --save` | 安装插件 |

本地写完文章后的发布流程：`hexo g` 生成 -> `git add .` -> `git commit` -> `git push`，推送后 Actions 自动完成部署。

## 用到的工具说明

### Hexo

Hexo 是一个基于 Node.js 的静态站点生成器。核心能力是把 Markdown 文件编译成完整的 HTML 静态网站。它通过插件体系扩展功能，支持模板渲染、代码高亮、分页、分类标签等。版本 8.x。

### Butterfly 主题

Butterfly 是 Hexo 最流行的主题之一，功能丰富且高度可配置。使用 Pug 模板引擎和 Stylus CSS 预处理器。内置支持暗黑模式、PJAX 加载、懒加载、代码高亮、数学公式、Mermaid 图表、本地搜索、多种评论系统等。

### hexo-deployer-git

Hexo 的 Git 部署插件，执行 `hexo deploy` 时自动把 `public/` 目录推送到配置的 Git 仓库。在 Actions 自动部署方案中其实可以不依赖它，但保留作为备选部署方式。

### hexo-generator-search

本地搜索插件，生成搜索索引文件，配合 Butterfly 的 `local_search` 功能实现站内全文搜索，无需外部服务。

### hexo-renderer-pug / hexo-renderer-stylus

Butterfly 主题的模板和样式渲染器。Pug 是一种简洁的 HTML 模板语法，Stylus 是一种灵活的 CSS 预处理器。缺少这两个插件主题会报错。

### GitHub Actions

GitHub 内置的 CI/CD 服务，在仓库中放置 YAML 工作流文件即可定义自动化流程。本文用它实现推送代码后自动安装依赖、编译 Hexo、发布到 Pages，全程免费。

### Giscus

一个开源的评论组件，利用 GitHub Discussions 作为评论存储后端。用户用 GitHub 账号登录即可评论，无需自建数据库或服务器。支持多语言和主题切换。

## 部署异常总结

以下是本次搭建过程中实际遇到的问题及解决方案，按时间顺序记录。

### 异常 1：Spawn failed / 连接超时

**现象**：执行 `hexo deploy` 或 `git push` 时报 `Spawn failed`，手动 push 报 `Failed to connect to github.com port 443 after 21102 ms: Timed out`。

**原因**：本地网络无法直连 GitHub，需要代理。

**解决**：开启 Clash 等代理工具，确保代理端口（如 7890/7897）处于开启状态。代理关闭后所有 git 操作都会超时。

### 异常 2：密码认证不被支持

**现象**：`fatal: Authentication failed`，提示 `Password authentication is not supported for Git operations`。

**原因**：GitHub 自 2021 年起不再支持密码方式推送，必须使用 Personal Access Token（PAT）。

**解决**：前往 GitHub Settings -> Developer settings -> Personal access tokens 创建 Token，勾选 `repo` 权限，推送时用 Token 替代密码。

### 异常 3：PAT 缺少 workflow 权限

**现象**：推送包含 `.github/workflows/deploy.yml` 的提交时报错：`refusing to allow a Personal Access Token to create or update workflow .github/workflows/deploy.yml without workflow scope`。

**原因**：修改工作流文件需要 Token 额外具备 `workflow` 权限，仅 `repo` 权限不够。

**解决**：重新创建 Token，同时勾选 `repo` 和 `workflow` 两个 scope。

### 异常 4：credential-manager 警告

**现象**：`git: 'credential-manager' is not a git command`，但推送仍能成功。

**原因**：Git 配置引用了旧版 credential manager 名称，新版改名为 `credential-manager-core`。

**解决**：属警告不影响功能，可忽略。如需消除，运行 `git config --global --unset credential.helper` 后重新设置。

### 异常 5：Node.js 20 弃用警告

**现象**：GitHub Actions 构建日志显示 `Node.js 20 is deprecated`，提示相关 Actions 被强制运行在 Node.js 24 上。

**原因**：GitHub 自 2025 年 9 月起弃用 Actions 运行器上的 Node.js 20。

**解决**：在 `deploy.yml` 中将 `node-version` 设为 `18`（稳定且兼容），避免使用已被弃用的版本。

### 异常 6：strip-ansi ESM 报错（ERR_REQUIRE_ESM）

**现象**：Actions 中 `npx hexo generate` 失败，报 `require() of ES Module .../strip-ansi/index.js ... not supported`。

**原因**：`strip-ansi` 新版本（v6+）改为纯 ESM 模块，而 Hexo 的 CommonJS 代码用 `require()` 引入它，在 Node.js 24 上不兼容。

**解决**：在 `package.json` 中添加 `overrides` 字段，把 `strip-ansi` 锁定到 CommonJS 版本：

```json
{
  "overrides": {
    "strip-ansi": "5.2.0"
  }
}
```

### 异常 7：主题被识别为 Git 子模块

**现象**：推送后 GitHub Pages 显示空白页，仓库中 `themes/butterfly` 显示为一个灰色文件夹图标，点击无法展开。

**原因**：`git clone` 主题时带入了 `.git` 目录，Git 把它当成了 submodule 引用而非普通文件，远程仓库里实际没有主题文件。

**解决**：删除 `themes/butterfly/.git` 目录，然后重新 `git add` 主题所有文件：

```bash
rmdir /s /q themes\butterfly\.git
git rm --cached themes/butterfly
git add themes/butterfly/
git commit -m "fix: add butterfly theme files directly"
git push
```

### 异常 8：deploy.yml YAML 语法错误

**现象**：GitHub Actions 报 `Invalid workflow file`，提示 `.github/workflows/deploy.yml` 第 9 行语法错误。

**原因**：YAML 缩进或格式有误。

**解决**：检查工作流文件的缩进，确保用空格而非 Tab，层级对齐。修正后重新推送。

### 异常 9：Pages 部署来源未切换

**现象**：推送代码后 Actions 构建成功，但网站内容不更新或显示空白页。

**原因**：GitHub Pages 的 Source 仍为默认的 "Deploy from a branch"，而工作流用的是 "GitHub Actions" 方式部署。

**解决**：进入仓库 Settings -> Pages -> Build and deployment，把 Source 改为 **GitHub Actions**。

### 异常 10：favicon 404

**现象**：浏览器标签页图标加载失败，控制台报 `GET /favicon.ico 404 Not Found`。

**原因**：`favicon` 配置路径指向了不存在的文件。

**解决**：在 `_config.butterfly.yml` 中把 `favicon` 指向实际存在的图片路径（如 `/img/avatar.jpg`），确保该文件在 `themes/butterfly/source/img/` 目录下。

## 小结

整个搭建过程涉及的前端工具链比较多，但每个组件各司其职：Hexo 负责生成、Butterfly 负责美化、GitHub 负责托管和自动化。最终的博客完全免费，且通过 Actions 实现了推送即部署的自动化流水线。踩过的坑主要集中在网络代理、Token 权限、主题子模块和 Node 版本兼容性这几方面，文中异常总结部分已逐一给出解决方案，供后续参考。
