# 个人静态博客实现计划

> **面向 AI 代理的工作者：** 必需子技能：使用 `superpowers:subagent-driven-development`（推荐）或 `superpowers:executing-plans` 逐任务实现此计划。步骤使用复选框（`- [ ]`）语法来跟踪进度。

**目标：** 基于 Astro 主题 `astro-koharu`，在 GitHub Pages 上部署十一的个人静态博客，配置温暖治愈风格、Markdown 写作、自动部署。

**架构：** 使用 `astro-koharu` 作为模板仓库，保留其主题能力与目录结构；通过 `config/site.yaml` 配置站点信息；使用 GitHub Actions 在 `push` 到 `main` 分支时构建并部署到 GitHub Pages。

**技术栈：** Astro 6.x、React、Tailwind CSS、pnpm、GitHub Pages、GitHub Actions。

---

## 文件结构

```
d:\trae_projects\L-blog\.worktrees\feature-blog/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions 部署工作流
├── config/
│   ├── site.yaml               # 站点主配置（标题、作者、导航、社交等）
│   └── i18n-content.yaml       # 内容翻译（保留主题默认）
├── src/
│   ├── content/
│   │   ├── blog/               # 博客文章目录
│   │   └── pages/              # 独立页面（关于、友链等）
│   ├── pages/                  # Astro 页面路由
│   └── components/             # 主题组件
├── public/
│   └── img/                    # 头像、封面等静态图片
├── astro.config.mjs            # Astro 配置（需适配 GitHub Pages base 路径）
├── package.json                # 依赖与脚本
├── pnpm-lock.yaml              # 锁定文件
├── tailwind.config.mjs         # Tailwind 配置
├── tsconfig.json               # TypeScript 配置
└── docs/
    └── superpowers/
        ├── specs/              # 设计规格文档
        └── plans/              # 本实现计划
```

## 前置依赖

- Node.js >= 22.12.0
- pnpm >= 10.28.2（推荐，因为主题使用 `packageManager: pnpm@10.28.2`）
- Git
- 一个 GitHub 仓库（例如 `sakayoriEleven/L-blog`）

---

## 任务 1：检查并安装 pnpm

**文件：** 无（环境准备）

- [ ] **步骤 1：检查 pnpm 版本**

运行：`pnpm --version`

预期输出：`10.28.2` 或更高版本

- [ ] **步骤 2：若未安装或版本过低，安装 pnpm**

运行：`corepack enable; corepack prepare pnpm@10.28.2 --activate`

Windows PowerShell 下运行：

```powershell
corepack enable
corepack prepare pnpm@10.28.2 --activate
```

- [ ] **步骤 3：验证 Node.js 版本**

运行：`node --version`

预期输出：`v22.12.0` 或更高

---

## 任务 2：获取 astro-koharu 主题模板

**文件：**
- 创建：worktree 根目录下除 `docs/` 和 `.git/` 外的主题文件
- 保留：`.worktrees/feature-blog/docs/superpowers/specs/2026-07-24-personal-blog-design.md`

- [ ] **步骤 1：进入 worktree 目录**

运行：`cd d:\trae_projects\L-blog\.worktrees\feature-blog`

- [ ] **步骤 2：使用 degit 下载主题模板（无 git 历史）**

运行：`npx degit cosZone/astro-koharu temp-theme --force`

- [ ] **步骤 3：复制主题文件到 worktree 根目录**

PowerShell 运行：

```powershell
Copy-Item -Path "temp-theme\*" -Destination "." -Recurse -Force
```

- [ ] **步骤 4：删除临时下载目录**

PowerShell 运行：`Remove-Item -Path "temp-theme" -Recurse -Force`

- [ ] **步骤 5：确认目录结构**

运行：`ls`

预期：看到 `config/`、`src/`、`public/`、`astro.config.mjs`、`package.json`、`pnpm-lock.yaml` 等文件，且 `docs/` 目录仍然存在。

---

## 任务 3：安装项目依赖

**文件：**
- 生成：`node_modules/`
- 生成：`.pnpm-store/`（pnpm 虚拟存储）

- [ ] **步骤 1：安装依赖**

运行：`pnpm install`

预期：成功安装所有依赖，无报错。

- [ ] **步骤 2：检查关键脚本**

运行：`pnpm run astro -- --version`

预期输出：Astro 版本号，例如 `Astro v6.4.8`

---

## 任务 4：配置站点信息

**文件：**
- 修改：`config/site.yaml`

- [ ] **步骤 1：备份原始配置**

运行：`Copy-Item config/site.yaml config/site.yaml.bak`

- [ ] **步骤 2：修改 `site` 基础信息**

将 `config/site.yaml` 中 `site` 段落修改为：

```yaml
site:
  title: 十一の小窝
  alternate: shiyi
  subtitle: 记录生活、技术与小确幸
  name: 十一
  description: 十一的个人博客，分享技术学习、生活随笔和作品展示
  avatar: /img/avatar.webp
  showLogo: true
  author: 十一
  url: https://Lowlft.github.io/L-blog
  defaultOgImage: /img/avatar.webp
  startYear: 2026
  timezone: Asia/Shanghai
  keywords:
    - 博客
    - Astro
    - 技术
    - 生活
    - 前端
```

- [ ] **步骤 3：关闭不需要的外部服务**

在 `config/site.yaml` 中，将 `analytics` 和 `comment` 段落改为：

```yaml
analytics:
  umami:
    enabled: false

comment:
  provider: none
```

- [ ] **步骤 4：简化国际化配置（可选）**

如果只想保留中文，将 `i18n` 段落改为：

```yaml
i18n:
  defaultLocale: zh
  locales:
    - code: zh
      label: 中文
```

- [ ] **步骤 5：修改社交链接**

将 `social` 段落改为十一自己的链接，例如：

```yaml
social:
  github:
    url: https://github.com/Lowlft
    icon: ri:github-fill
    color: '#191717'
  email:
    url: mailto:saturnalorbit@163.com
    icon: ri:mail-line
    color: '#55acd5'
  rss:
    url: /rss.xml
    icon: ri:rss-line
    color: '#ff6600'
```

---

## 任务 5：适配 GitHub Pages

**文件：**
- 修改：`astro.config.mjs`
- 创建：`.github/workflows/deploy.yml`

- [ ] **步骤 1：修改 Astro 配置，添加 `base` 路径**

打开 `astro.config.mjs`，在 `defineConfig` 的参数对象中添加 `base` 字段：

```javascript
export default defineConfig({
  site: yamlConfig.site.url,
  base: '/L-blog',
  compressHTML: true,
  // ... 其余配置保持不变
});
```

如果仓库名为 `sakayoriEleven.github.io`（用户主页仓库），则 `base` 应省略或设为 `'/'`。

- [ ] **步骤 2：创建 GitHub Actions 部署工作流**

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup pnpm
        uses: pnpm/action-setup@v4
        with:
          version: 10.28.2

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 22.12.0
          cache: pnpm

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Build site
        run: pnpm run build

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **步骤 3：添加空 `.nojekyll` 文件（避免 GitHub Pages 使用 Jekyll 处理）**

创建空文件：

```powershell
New-Item -Path "public\.nojekyll" -ItemType File -Force | Out-Null
```

---

## 任务 6：清理示例内容

**文件：**
- 删除：`src/content/blog/` 下的示例文章
- 删除：`src/content/pages/` 下不需要的示例页面
- 删除：`config/site.yaml` 中的示例友链

- [ ] **步骤 1：查看示例文章**

运行：`ls src/content/blog/`

- [ ] **步骤 2：删除示例文章（保留目录）**

PowerShell 运行：

```powershell
Remove-Item -Path "src\content\blog\*" -Recurse -Force
```

- [ ] **步骤 3：查看示例页面**

运行：`ls src/content/pages/`

- [ ] **步骤 4：保留 `about.md` 并清空示例内容，或删除后重建**

如果存在 `src/content/pages/about.md`，打开并替换内容为：

```markdown
---
title: 关于我
description: 了解十一
---

你好呀，这里是十一的个人博客 ✨

我会在这里记录技术学习、生活随笔和一些小小的作品。

希望能给你带来一点点温暖和灵感～
```

如果不存在，则创建该文件。

- [ ] **步骤 5：清理示例友链**

将 `config/site.yaml` 中 `friends.data` 改为空数组或只保留一个示例：

```yaml
friends:
  intro:
    title: 友情链接
    subtitle: 欢迎交换友链！
    applyTitle: 申请友链
    applyDesc: 请在本页留言，格式如下
    exampleYaml: |
      - site: 你的博客名称
        url: https://your-blog.com/
        owner: 你的昵称
        desc: 站点简介
        image: https://your-blog.com/avatar.jpg
        color: "#ffc0cb"
  data: []
```

---

## 任务 7：添加十一的首批内容

**文件：**
- 创建：`src/content/blog/life/hello-world.md`
- 创建：`src/content/blog/note/my-first-tech-post.md`
- 创建/修改：`src/content/pages/about.md`
- 创建：`public/img/avatar.webp`

- [ ] **步骤 1：创建第一篇生活随笔**

创建 `src/content/blog/life/hello-world.md`：

```markdown
---
title: 你好，世界
description: 十一的博客开张啦
pubDate: 2026-07-24
tags:
  - 随笔
  - 生活
category: 随笔
---

这是十一的第一篇博客文章 🎉

以后这里会记录技术学习、生活点滴和一些胡思乱想。

希望能和看到这里的你，一起慢慢成长～
```

- [ ] **步骤 2：创建第一篇技术笔记**

创建 `src/content/blog/note/my-first-tech-post.md`：

```markdown
---
title: 我的第一篇技术笔记
description: 记录用 Astro 搭建博客的过程
pubDate: 2026-07-24
tags:
  - Astro
  - 博客
  - 前端
category: 笔记
---

今天用 Astro + astro-koharu 主题搭好了个人博客，整个过程比想象中顺利。

## 为什么选择 Astro？

Astro 是静态站点生成器，非常适合内容型网站，加载速度快，主题生态丰富。

## 后续计划

- 写更多技术文章
- 分享生活随笔
- 慢慢打磨博客样式

期待这里变得越来越温暖 ✨
```

- [ ] **步骤 3：添加头像**

准备一张方形头像图片，保存为 `public/img/avatar.webp`。

如果没有现成头像，可以先使用一个占位图片：

```powershell
Invoke-WebRequest -Uri "https://api.dicebear.com/7.x/avataaars/svg?seed=shiyi" -OutFile "public\img\avatar.svg"
```

如果主题只支持 `webp`，则需要转换为 `webp` 格式，或修改 `config/site.yaml` 中的 `avatar` 路径为 `/img/avatar.svg`。

---

## 任务 8：本地预览与验证

**文件：** 无

- [ ] **步骤 1：启动本地开发服务器**

运行：`pnpm run dev`

预期：服务启动成功，提示访问 `http://localhost:4321`（或类似地址）。

- [ ] **步骤 2：访问首页**

打开浏览器访问 `http://localhost:4321/L-blog/`（如果配置了 base）或 `http://localhost:4321/`。

预期：看到十一的博客首页，显示站点标题、头像、最近文章列表。

- [ ] **步骤 3：检查文章详情页**

点击一篇文章，进入详情页。

预期：文章正常渲染，标题、日期、标签、正文显示正确。

- [ ] **步骤 4：检查黑暗模式切换**

点击页面上的主题切换按钮。

预期：页面在浅色和深色模式之间切换，无明显异常。

- [ ] **步骤 5：停止开发服务器**

按 `Ctrl + C` 停止。

---

## 任务 9：生产构建验证

**文件：**
- 生成：`dist/` 目录

- [ ] **步骤 1：执行生产构建**

运行：`pnpm run build`

预期：构建成功，输出 `dist/` 目录。

- [ ] **步骤 2：检查 dist 目录内容**

运行：`ls dist`

预期：包含 `index.html`、`_astro/`、`img/`、`rss.xml` 等文件。

- [ ] **步骤 3：本地预览生产构建（可选）**

运行：`pnpm run preview`

打开浏览器访问对应的预览地址，确认页面正常。

---

## 任务 10：提交并推送代码

**文件：**
- 提交：worktree 中所有变更

- [ ] **步骤 1：检查变更**

运行：`git status`

- [ ] **步骤 2：添加所有文件到暂存区**

运行：`git add .`

- [ ] **步骤 3：提交**

运行：

```bash
git commit -m "feat: init personal blog with astro-koharu theme

- Add astro-koharu theme template
- Configure site info for 十一
- Add GitHub Pages deployment workflow
- Add initial posts and about page"
```

- [ ] **步骤 4：推送到 GitHub**

运行：`git push -u origin feature/blog`

- [ ] **步骤 5：在 GitHub 创建 Pull Request 或直接合并到 main**

如果希望直接部署，将 `feature/blog` 合并到 `main`：

```bash
git checkout main
git merge feature/blog
git push origin main
```

---

## 任务 11：配置 GitHub Pages

**文件：** 无（GitHub 仓库设置）

- [ ] **步骤 1：打开 GitHub 仓库设置**

访问：`https://github.com/Lowlft/L-blog/settings/pages`

- [ ] **步骤 2：选择部署源**

在 "Build and deployment" 中：
- Source: `GitHub Actions`

- [ ] **步骤 3：触发部署**

确保 `main` 分支有最新代码后，GitHub Actions 会自动触发部署。

- [ ] **步骤 4：查看部署状态**

访问：`https://github.com/Lowlft/L-blog/actions`

等待 `Deploy to GitHub Pages` 工作流完成。

- [ ] **步骤 5：访问线上博客**

访问：`https://Lowlft.github.io/L-blog/`

预期：看到博客首页。

---

## 任务 12：后续优化清单（二期）

以下任务不在本次实现范围内，但可在博客上线后逐步完成：

- [ ] 配置自定义域名（如有）
- [ ] 启用评论系统（Giscus 最简单，只需 GitHub Discussions）
- [ ] 调整配色为更温暖的色调
- [ ] 添加更多关于我页面内容
- [ ] 配置友链
- [ ] 优化 SEO 和 Open Graph 图片
- [ ] 添加站点统计（可选）

---

## 自检

- [ ] **规格覆盖度：** 设计规格中的所有 MVP 需求（文章列表、标签、关于页、黑暗模式、图片灯箱、Markdown、响应式、SEO、GitHub Actions 部署）都有对应任务。
- [ ] **占位符扫描：** 计划中没有 TODO、待定、后续实现等模糊表述；所有命令和代码都是具体的。
- [ ] **类型一致性：** 文件路径、配置字段、脚本名称前后一致。
- [ ] **可行性检查：** 步骤粒度控制在 2-5 分钟；先环境准备、再模板获取、再配置、再内容、最后部署，符合依赖顺序。
