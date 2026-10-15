# Fuwari Blog (定制版)

一个开箱即用的 **Astro 静态博客工程**，基于 [Fuwari](https://github.com/saicaca/fuwari) 主题深度定制：中文内容、可配置头像/横幅/社交链接、分类/标签/归档/全文搜索，构建产物是纯静态文件，**任何静态托管都能跑**——低配 VPS + Caddy、Cloudflare Pages、Vercel 均可。

> 配套发布工具：[blog-publish-tool](https://github.com/lovehaichina/blog-publish-tool) —— Windows 一键构建上传，无需安装 Node。
>
> 原项目：[saicaca/fuwari](https://github.com/saicaca/fuwari) · MIT License © 2024 saicaca

![GitHub stars](https://img.shields.io/github/stars/lovehaichina/fuwari-blog) ![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg) ![Astro](https://img.shields.io/badge/Astro-5.x-black) ![Type](https://img.shields.io/badge/type-static--site-green)

---

## 为什么需要它？

- **写博客不该先折腾环境**：很多主题依赖复杂构建链，配置半天才能本地预览。
- **内容要自己掌控**：文章是纯 Markdown 存在本地，不绑定任何平台，随时迁移。
- **低配服务器也能扛**：静态站点无后端、无数据库，128MB 的 VPS 即可长期运行。
- **发布要简单**：配合配套工具，双击即可完成 构建 → 上传 → 上线，不用记命令。

## 功能特性

- **纯静态构建**：Astro `output: static`，构建产物（dist/）可直接上传任意静态服务器。
- **中文界面**：全中文布局与内容结构，分类/标签/归档/关于页齐全。
- **全文搜索**：内置 pagefind 搜索，无需第三方服务。
- **可视化配置**：标题、副标题、横幅、头像、社交链接、导航全部在 `src/config.ts` 一处配置。
- **图片/视频/附件**：统一放 `public/`，Markdown 直接引用，格式不限（png/jpg/webp/avif/mp4 等）。
- **草稿机制**：frontmatter `draft: true/false` 控制发布状态。
- **零后端零数据库**：内容即文件，备份 = 拷贝目录。

## 快速开始

### 环境要求

| 项 | 要求 |
|---|---|
| Node.js | 18+（推荐 20+） |
| 包管理器 | pnpm（推荐）或 npm |
| 服务器 | 任意静态托管；低配 VPS（128MB+）即可 |

### 本地开发

```bash
git clone https://github.com/lovehaichina/fuwari-blog.git && cd fuwari-blog

# 安装依赖（首次）
pnpm install

# 本地预览（http://localhost:4321）
pnpm dev

# 构建静态站点（输出到 dist/）
pnpm build
```

### 部署方式一：VPS + Caddy（推荐，自动 HTTPS）

```bash
# 构建
pnpm build

# 上传 dist/ 到服务器（示例：/var/www/html）
scp -r dist/* root@YOUR_SERVER_IP:/var/www/html/
```

`/etc/Caddyfile`：

```
blog.example.com {
    root * /var/www/html
    file_server
    encode zstd gzip
}
```

```bash
caddy reload   # 自动申请 HTTPS 证书
```

> 嫌手动上传麻烦？用配套的 [blog-publish-tool](https://github.com/lovehaichina/blog-publish-tool) 双击一键完成构建 + 上传。

### 部署方式二：Cloudflare Pages / Vercel

- 仓库已含 `vercel.json`，Vercel 导入即可（构建命令 `pnpm build`，输出目录 `dist`）
- Cloudflare Pages 同样支持 Astro 静态构建，直接连接仓库自动部署

## 配置详解

所有站点配置集中在 **`src/config.ts`**：

| 配置块 | 字段 | 说明 |
|---|---|---|
| `siteConfig` | `title` / `subtitle` | 站点标题、副标题 |
| | `lang` | 语言（`zh_CN` / `en` 等） |
| | `themeColor.hue` | 主题色（0-360，如蓝 250、粉 345） |
| | `banner` | 横幅图开关/路径/位置 |
| | `toc` | 文章目录开关/层级 |
| `navBarConfig` | `links` | 导航链接（首页/归档/关于，可增自定义页） |
| `profileConfig` | `avatar` / `name` / `bio` | 头像、昵称、个人简介 |
| | `links` | 社交链接（图标 + 链接，内置 fa6/material-symbols 图标集） |
| `licenseConfig` | `name` / `url` | 页脚版权声明 |

## 写文章指南

文章写在 **`src/content/posts/*.md`**，frontmatter 示例：

```yaml
---
title: 我的第一篇文章
published: 2026-10-15        # 发布日期 YYYY-MM-DD
description: 列表页显示的一句话摘要
tags: [技术, 随笔]            # 可多个
category: 随笔                # 只能一个
draft: true                   # true=草稿不发布；false 或删掉=发布
---
```

**多媒体怎么放**（文件放 `public/`，路径以 `/` 开头引用）：

```
public/images/  →  ![图片说明](/images/xxx.png)
public/videos/  →  <video controls src="/videos/xxx.mp4"></video>
public/files/   →  [下载文件](/files/xxx.pdf)
```

> 图片格式不限：png / jpg / jpeg / gif / svg / webp / avif 都支持。大视频建议外链 B 站/YouTube 后嵌 iframe。

## 目录结构

```
fuwari-blog/
├── src/
│   ├── config.ts          # 站点核心配置（标题/头像/社交链接/导航）
│   ├── content/posts/     # Markdown 文章（frontmatter 控制发布）
│   ├── layouts/           # 页面布局
│   ├── components/        # 组件
│   └── pages/             # 页面路由（首页/归档/关于/搜索）
├── public/                # 静态资源（图片/视频/附件/favicon）
├── astro.config.mjs       # Astro 配置
├── tailwind.config.cjs    # 样式
├── pagefind.yml           # 全文搜索配置
└── package.json
```

## 二次开发

技术栈：**Astro 5 + TypeScript + Tailwind CSS + Svelte 组件**，结构清晰，上手快：

```bash
# 改主题颜色 / 布局：编辑 src/components 与 src/layouts
# 新增页面：在 src/pages 加 .astro 文件
# 改样式：tailwind.config.cjs + src/styles
```

- Fork 后即可自由定制，欢迎提交 PR
- 需要给文章页加评论？可接入 giscus / waline / twikoo（社区成熟方案，按需自选）
- 想换字体/深色模式细节：`src/config.ts` 与主题样式文件都有现成开关

## 常见问题

**Q：pnpm 安装失败？**
Node 版本过低或网络问题。升级 Node 20+，或换 npm：`npm install`（锁定文件已提供，依赖一致）。

**Q：dist 目录是空的？**
构建未成功。先跑 `pnpm build` 看报错；成功会在工程根生成 `dist/`。

**Q：文章发布后不显示？**
检查 frontmatter：`draft` 是否为 `true`（草稿不发布）；`published` 日期是否为未来时间。

**Q：头像/横幅不显示？**
路径写法：`src/config.ts` 中相对路径指向 `src/assets/`，以 `/` 开头指向 `public/`；确认文件确实存在。

**Q：迁移到别的博客平台？**
内容全在 `src/content/posts/`，Markdown 通用格式，导出后几乎零成本迁移。

## License

MIT License © 2024 saicaca（本仓库为 Fuwari 的派生定制版，保留原作者版权声明；详见 [LICENSE](LICENSE)）
