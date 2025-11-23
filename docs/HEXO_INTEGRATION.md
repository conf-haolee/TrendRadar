# 将 TrendRadar 嵌入到 Hexo 博客

本文档详细介绍如何将 TrendRadar 新闻聚合页面集成到您的 Hexo 个人博客中。

## 目录

- [前提条件](#前提条件)
- [方案概述](#方案概述)
- [方案一：iframe 嵌入（推荐）](#方案一iframe-嵌入推荐)
- [方案二：部署到子目录](#方案二部署到子目录)
- [方案三：自定义 Hexo 页面](#方案三自定义-hexo-页面)
- [常见问题](#常见问题)

## 前提条件

在开始集成之前，请确保：

1. 您已经有一个运行中的 Hexo 博客（例如：`conf-haolee.github.io`）
2. 您已经 Fork 了 TrendRadar 项目并配置好 GitHub Actions
3. **TrendRadar 的 GitHub Pages 已经启用并正常工作**

### 如何验证 TrendRadar 是否正常工作？

在集成到 Hexo 之前，请先确认 TrendRadar 本身能正常访问：

1. **启用 GitHub Pages**
   - 进入您 fork 的 TrendRadar 仓库
   - 点击 Settings → Pages
   - Source 选择 "Deploy from a branch"
   - Branch 选择 "main"，目录选择 "/ (root)"
   - 保存后等待 1-2 分钟

2. **运行爬虫生成数据**
   - 配置 `config/config.yaml` 和 `config/frequency_words.txt`
   - 进入 Actions → Hot News Crawler → Run workflow 手动运行一次
   - 等待工作流完成（显示绿色对勾）

3. **验证页面可访问**
   - 访问 `https://您的用户名.github.io/TrendRadar/`
   - 应该能看到热点新闻分析页面
   - **如果此步骤失败，请先解决 TrendRadar 的配置问题**

## 方案概述

根据您的需求和技术能力，有以下三种集成方案：

| 方案 | 难度 | 优点 | 缺点 |
|------|------|------|------|
| iframe 嵌入 | ⭐ 简单 | 配置简单，维护方便 | 需要处理跨域和响应式 |
| 部署到子目录 | ⭐⭐ 中等 | 完全集成，SEO 友好 | 需要配置构建流程 |
| 自定义页面 | ⭐⭐⭐ 复杂 | 完全控制样式 | 需要自定义开发 |

## 方案一：iframe 嵌入（推荐）

这是最简单的集成方式，适合大多数用户。

### 步骤 1：创建 Hexo 页面

在您的 Hexo 博客根目录下执行：

```bash
hexo new page trendradar
```

### 步骤 2：编辑页面内容

编辑 `source/trendradar/index.md` 文件：

```markdown
---
title: 热点新闻聚合
date: 2025-11-23
comments: false
---

<div style="width: 100%; height: 100vh; min-height: 800px;">
  <iframe 
    src="https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/" 
    style="width: 100%; height: 100%; border: none;"
    title="TrendRadar 新闻聚合"
    loading="lazy">
  </iframe>
</div>

<style>
  /* 移除 Hexo 默认的内容边距，让 iframe 全屏显示 */
  .post-body {
    padding: 0 !important;
  }
  .content {
    padding: 0 !important;
  }
</style>
```

**注意**：将 `YOUR_GITHUB_USERNAME` 替换为您的 GitHub 用户名（例如：`conf-haolee`）

### 步骤 3：添加到导航菜单

编辑 Hexo 主题配置文件（通常是 `_config.yml` 或 `themes/你的主题/_config.yml`），添加菜单项：

```yaml
menu:
  首页: /
  归档: /archives
  热点新闻: /trendradar  # 新增这一行
  关于: /about
```

### 步骤 4：部署

```bash
hexo clean
hexo generate
hexo deploy
```

### 优点
- ✅ 配置简单，5 分钟即可完成
- ✅ TrendRadar 和 Hexo 博客独立维护
- ✅ TrendRadar 更新自动同步

### 缺点
- ❌ iframe 在某些移动设备上可能显示不佳
- ❌ SEO 优化有限

## 方案二：部署到子目录

将 TrendRadar 作为 Hexo 博客的一个子目录部署。

### 步骤 1：修改 TrendRadar 仓库

在您 fork 的 TrendRadar 仓库中，修改 `.github/workflows/crawler.yml`：

```yaml
      - name: Commit and push if changes
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          git add -A
          git diff --quiet && git diff --staged --quiet || (git commit -m "Auto update by GitHub Actions at $(TZ=Asia/Shanghai date)" && git push)
          
      # 新增：将生成的页面推送到 Hexo 博客仓库
      - name: Deploy to Hexo blog
        env:
          HEXO_REPO_TOKEN: ${{ secrets.HEXO_REPO_TOKEN }}
        run: |
          # 克隆您的 Hexo 博客仓库（请替换为您的仓库地址）
          git clone https://${HEXO_REPO_TOKEN}@github.com/YOUR_USERNAME/YOUR_BLOG_REPO.git hexo-blog
          
          # 创建 trendradar 目录
          mkdir -p hexo-blog/source/trendradar
          
          # 复制 TrendRadar 生成的文件
          cp index.html hexo-blog/source/trendradar/
          cp -r output hexo-blog/source/trendradar/
          
          # 提交并推送
          cd hexo-blog
          git config user.name 'TrendRadar Bot'
          git config user.email 'bot@trendradar.com'
          git add source/trendradar
          git commit -m "Update TrendRadar: $(TZ=Asia/Shanghai date)" || exit 0
          git push
```

### 步骤 2：配置 GitHub Token

1. 在 GitHub 上创建 Personal Access Token（需要 `repo` 权限）
2. 在 TrendRadar 仓库的 Settings → Secrets → Actions 中添加 `HEXO_REPO_TOKEN`

### 步骤 3：配置 Hexo 跳过渲染

编辑 Hexo 根目录下的 `_config.yml`：

```yaml
skip_render:
  - trendradar/**
```

这样 Hexo 就不会处理 TrendRadar 的 HTML 文件。

### 步骤 4：添加导航链接

在主题配置中添加菜单项，指向 `/trendradar/`。

### 优点
- ✅ 完全集成到博客中
- ✅ 统一的域名和路径
- ✅ SEO 友好

### 缺点
- ❌ 配置相对复杂
- ❌ 需要配置 GitHub Token
- ❌ TrendRadar 更新需要触发 Hexo 重新构建

## 方案三：自定义 Hexo 页面

创建一个自定义的 Hexo 页面模板，直接嵌入 TrendRadar 的 HTML 代码。

### 步骤 1：创建自定义布局

在您的 Hexo 主题目录下创建 `layout/trendradar.ejs`：

```ejs
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>热点新闻分析 - <%= config.title %></title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <%- partial('_partial/head') %>
    <style>
        /* 这里粘贴 TrendRadar 的 CSS */
    </style>
</head>
<body>
    <%- partial('_partial/header') %>
    
    <!-- 这里粘贴 TrendRadar 的 HTML 内容 -->
    <div class="container">
        <!-- TrendRadar 内容 -->
    </div>
    
    <%- partial('_partial/footer') %>
    
    <script>
        /* 这里粘贴 TrendRadar 的 JavaScript */
    </script>
</body>
</html>
```

### 步骤 2：创建数据获取脚本

创建 Hexo 插件或脚本，定期从 TrendRadar API 获取数据并生成页面。

### 步骤 3：配置页面

创建 `source/trendradar/index.md`：

```markdown
---
layout: trendradar
title: 热点新闻聚合
---
```

### 优点
- ✅ 完全控制样式和行为
- ✅ 可以与 Hexo 主题深度整合

### 缺点
- ❌ 实现复杂，需要较强的前端开发能力
- ❌ 需要手动同步 TrendRadar 的更新
- ❌ 维护成本高

## 推荐方案对比

根据不同的使用场景，推荐方案如下：

### 快速集成（推荐新手）
**选择方案一**：iframe 嵌入
- 适合：只想快速在博客中展示 TrendRadar
- 优势：5 分钟配置完成，零维护成本

### 专业集成（推荐进阶用户）
**选择方案二**：部署到子目录
- 适合：希望将 TrendRadar 作为博客的一部分
- 优势：SEO 友好，用户体验好

### 深度定制（推荐开发者）
**选择方案三**：自定义页面
- 适合：需要深度定制样式和功能
- 优势：完全控制，可以添加自定义功能

## 常见问题

### Q1: iframe 显示空白或无法加载内容？

**A**: 请按以下步骤排查：

1. **先测试 TrendRadar 本身**
   - 直接访问 `https://您的用户名.github.io/TrendRadar/`
   - 如果这里也是空白，说明 TrendRadar 本身有问题：
     - 检查 Settings → Pages 是否已启用
     - 检查是否运行过爬虫工作流（Actions → Hot News Crawler）
     - 确认仓库根目录有 `index.html` 文件

2. **检查 GitHub Pages 配置**
   - 分支必须选择 "main"
   - 目录必须选择 "/ (root)"
   - 等待 GitHub Pages 部署完成（1-2 分钟）
   - 查看 Actions 中的 "pages build and deployment" 是否成功

3. **检查 iframe 配置**
   - 确认 src 地址拼写正确（注意仓库名大小写）
   - 检查浏览器控制台是否有错误信息
   - 尝试添加时间戳避免缓存：`src="https://...?t=123"`

4. **运行爬虫生成数据**
   - 进入 Actions → Hot News Crawler → Run workflow
   - 等待工作流完成
   - 确认有新的 commit 生成

### Q2: iframe 嵌入后页面显示不完整？

**A**: 调整 iframe 的高度：

```html
<iframe 
  src="https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/" 
  style="width: 100%; height: 120vh; border: none;"
  title="TrendRadar 新闻聚合">
</iframe>
```

### Q2: 如何让 iframe 自适应高度？

**A**: 添加以下 JavaScript 代码：

```html
<script>
window.addEventListener('message', function(e) {
  if (e.origin === 'https://YOUR_GITHUB_USERNAME.github.io') {
    var iframe = document.querySelector('iframe');
    if (e.data.height) {
      iframe.style.height = e.data.height + 'px';
    }
  }
});
</script>
```

同时在 TrendRadar 的 `index.html` 中添加：

```javascript
// 在页面加载完成后
window.parent.postMessage({
  height: document.body.scrollHeight
}, '*');
```

### Q3: TrendRadar 更新后博客没有同步？

**A**: 
- 如果使用方案一（iframe），清除浏览器缓存即可
- 如果使用方案二（子目录），需要重新运行 TrendRadar 的 GitHub Actions

### Q4: 显示的不是最新数据？

**A**: 
- TrendRadar 需要定期运行爬虫更新数据
- 爬虫默认每小时自动运行一次（通过 GitHub Actions 的 cron 触发）
- 可以手动触发更新：Actions → Hot News Crawler → Run workflow
- 等待工作流完成后，数据会自动推送到 GitHub Pages
- 如果使用 iframe 方案，刷新页面或清除缓存即可看到最新数据

### Q5: 可以自定义 TrendRadar 的样式吗？

**A**: 
- 方案一：可以通过 CSS 覆盖 iframe 内的样式（受限）
- 方案二：可以直接修改 TrendRadar 的样式文件
- 方案三：完全自定义

### Q6: 如何在博客首页展示最新热点？

**A**: 可以使用 Hexo 插件或自定义组件，通过 AJAX 获取 TrendRadar 数据：

```javascript
fetch('https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/output/最新日期/txt/report.txt')
  .then(response => response.text())
  .then(data => {
    // 解析并显示数据
    document.getElementById('trending-news').innerHTML = parseNews(data);
  });
```

## 进阶技巧

### 1. 添加刷新按钮

在 Hexo 页面中添加刷新 TrendRadar 的按钮：

```html
<button onclick="document.querySelector('iframe').contentWindow.location.reload()">
  刷新新闻
</button>
```

### 2. 响应式优化

针对移动端优化显示：

```css
@media (max-width: 768px) {
  iframe {
    height: 100vh !important;
  }
}
```

### 3. 添加加载动画

```html
<div id="loading" style="text-align: center; padding: 50px;">
  <p>加载中...</p>
</div>
<iframe 
  src="https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/" 
  onload="document.getElementById('loading').style.display='none'"
  style="display: none; width: 100%; height: 100vh; border: none;">
</iframe>

<script>
document.querySelector('iframe').addEventListener('load', function() {
  this.style.display = 'block';
});
</script>
```

## 总结

选择适合您的方案：

1. **快速简单** → 方案一（iframe 嵌入）
2. **专业集成** → 方案二（子目录部署）  
3. **深度定制** → 方案三（自定义页面）

推荐大多数用户使用**方案一**，可以在最短时间内实现集成，后续如有需要再升级到方案二。

## 相关链接

- [TrendRadar 主项目](https://github.com/sansan0/TrendRadar)
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
- [GitHub Pages 文档](https://docs.github.com/cn/pages)

## 需要帮助？

如果在集成过程中遇到问题，可以：

1. 查看 [TrendRadar 问题答疑](https://github.com/sansan0/TrendRadar#问题答疑与交流)
2. 提交 Issue 到 [TrendRadar 仓库](https://github.com/sansan0/TrendRadar/issues)
3. 参考 Hexo 社区讨论
