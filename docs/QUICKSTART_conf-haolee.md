# 快速开始：为 conf-haolee.github.io 集成 TrendRadar

本文档是专门为 `conf-haolee.github.io` 博客集成 TrendRadar 的快速指南。

## ⚠️ 前提条件（必须先完成）

在开始集成之前，请确保：

1. **已经 Fork 了 TrendRadar 项目**
   - 访问 https://github.com/sansan0/TrendRadar 并点击 Fork
   - Fork 后的仓库地址应该是：https://github.com/conf-haolee/TrendRadar

2. **已启用 GitHub Pages**
   - 进入您 fork 的 TrendRadar 仓库
   - 点击 Settings → Pages（左侧菜单）
   - 在 "Build and deployment" 部分：
     - Source: 选择 "Deploy from a branch"
     - Branch: 选择 "main" 分支，目录选择 "/ (root)"
     - 点击 Save 保存
   - 等待 1-2 分钟，页面上方会显示 "Your site is live at https://conf-haolee.github.io/TrendRadar/"

3. **已配置并运行过爬虫**
   - 在 TrendRadar 仓库中配置 `config/config.yaml` 和 `config/frequency_words.txt`
   - 运行一次 GitHub Actions 工作流（Actions → Hot News Crawler → Run workflow）
   - 等待工作流完成，确认生成了 `index.html` 和 `output/` 目录

4. **验证 TrendRadar 是否正常工作**
   - 访问 https://conf-haolee.github.io/TrendRadar/
   - 应该能看到热点新闻分析页面
   - **如果这一步看不到内容，请先解决 TrendRadar 本身的配置问题，再继续集成**

## 🎯 最简单的方式（推荐）：iframe 嵌入

确认上述前提条件都满足后，只需 3 步，5 分钟内完成：

### 步骤 1️⃣：创建 TrendRadar 页面

在您的 Hexo 博客根目录执行：

```bash
hexo new page trendradar
```

### 步骤 2️⃣：编辑页面内容

打开 `source/trendradar/index.md`，将内容替换为：

```markdown
---
title: 热点新闻聚合
date: 2025-11-23
comments: false
---

<div style="width: 100%; height: 100vh; min-height: 800px;">
  <iframe 
    src="https://conf-haolee.github.io/TrendRadar/" 
    style="width: 100%; height: 100%; border: none;"
    title="TrendRadar 新闻聚合"
    loading="lazy">
  </iframe>
</div>

<style>
  .post-body {
    padding: 0 !important;
  }
  .content {
    padding: 0 !important;
  }
</style>
```

### 步骤 3️⃣：添加导航菜单并部署

在您的 Hexo 主题配置文件中（`_config.yml` 或 `themes/你的主题/_config.yml`）添加菜单项：

```yaml
menu:
  首页: /
  归档: /archives
  热点新闻: /trendradar  # 👈 添加这一行
  关于: /about
```

然后部署：

```bash
hexo clean
hexo generate
hexo deploy
```

## 🎉 完成！

现在访问 `https://conf-haolee.github.io/trendradar/` 即可查看嵌入的 TrendRadar 页面。

## ⏱️ 关于数据更新机制

**重要说明：TrendRadar 不是实时更新系统**

TrendRadar 通过 GitHub Actions 定时运行爬虫来更新数据，而不是实时刷新：

### 📊 更新频率

- **默认更新频率**：每小时一次（通过 GitHub Actions 的 cron 自动触发）
- **更新时间**：每小时的整点（如 8:00、9:00、10:00...）
- **实际时间**：GitHub Actions 可能有 5-10 分钟的延迟

### 🔄 如何查看最新数据

**方法一：等待自动更新**
- 爬虫会每小时自动运行
- 查看 Actions 标签页，确认 "Hot News Crawler" 工作流是否在运行
- 工作流完成后，刷新页面即可看到新数据

**方法二：手动触发更新**
1. 进入您的 TrendRadar 仓库：https://github.com/conf-haolee/TrendRadar
2. 点击 Actions 标签页
3. 选择 "Hot News Crawler" 工作流
4. 点击右侧的 "Run workflow" 按钮
5. 选择分支 "main"，点击 "Run workflow"
6. 等待 2-3 分钟让工作流完成
7. 刷新您的页面 `https://conf-haolee.github.io/trendradar/`

### 📅 查看数据时间戳

TrendRadar 页面的头部会显示"生成时间"，这是数据的最后更新时间，而不是当前时刻。

### ⚙️ 自定义更新频率（可选）

如果您想改变更新频率，可以修改 `.github/workflows/crawler.yml` 文件中的 cron 表达式：

```yaml
schedule:
  - cron: "0 * * * *"    # 每小时一次（默认）
  # - cron: "*/30 * * * *"  # 每30分钟一次
  # - cron: "0 */2 * * *"   # 每2小时一次
```

**注意**：不建议设置过于频繁（如小于 30 分钟），以避免被 GitHub 限制资源。

## 📱 进阶优化（可选）

如果想要更好的体验，可以使用增强版本的页面模板。参考项目中的示例文件：

```bash
# 复制示例文件内容到您的页面
cat TrendRadar/docs/hexo-page-example.md
```

这个增强版本包含：
- ✅ 加载动画
- ✅ 错误处理
- ✅ 响应式优化
- ✅ 超时重试

## 🔧 更多集成方案

如果您想要更深度的集成（例如部署到子目录、自定义样式等），请参考完整文档：

- [中文完整文档](HEXO_INTEGRATION.md)
- [English Documentation](HEXO_INTEGRATION_EN.md)

## ❓ 遇到问题？

常见问题：

**Q: iframe 显示空白或无法加载？**  
A: 请按以下步骤排查：
1. 先访问 https://conf-haolee.github.io/TrendRadar/ 确认 TrendRadar 本身是否正常显示
2. 如果 TrendRadar 页面本身显示空白：
   - 检查是否已在 Settings → Pages 中启用 GitHub Pages
   - 检查是否已运行过爬虫工作流（Actions → Hot News Crawler）
   - 检查工作流是否成功完成（绿色对勾）
   - 查看仓库根目录是否有 `index.html` 文件
3. 如果 TrendRadar 页面正常但 iframe 中不显示：
   - 检查浏览器控制台是否有跨域错误
   - 确认 iframe src 地址拼写正确（注意大小写：TrendRadar）
   - 尝试在 iframe src 后添加时间戳避免缓存：`src="https://conf-haolee.github.io/TrendRadar/?t=123"`

**Q: 显示的不是最新数据？**  
A: TrendRadar 需要定期运行爬虫更新数据：
- 爬虫默认每小时自动运行一次（通过 GitHub Actions）
- 可以手动触发：进入 Actions → Hot News Crawler → Run workflow
- 等待工作流完成后，数据会自动更新到 GitHub Pages

**Q: GitHub Pages 显示 404？**  
A: 
1. 确认已在 Settings → Pages 中正确配置
2. 分支选择 "main"，目录选择 "/ (root)"
3. 等待几分钟让 GitHub Pages 构建完成
4. 检查 Actions 标签页中的 "pages build and deployment" 工作流是否成功

**Q: 页面高度不够？**  
A: 调整 iframe 的 `height` 样式，例如改为 `120vh`。

**Q: 想要自定义样式？**  
A: 参考[完整文档](HEXO_INTEGRATION.md)中的方案二或方案三。

## 📚 相关资源

- [TrendRadar 项目主页](https://github.com/sansan0/TrendRadar)
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
- [示例页面模板](hexo-page-example.md)
- [GitHub Actions 自动部署](hexo-deployment-workflow.yml)
