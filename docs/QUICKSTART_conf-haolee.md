# 快速开始：为 conf-haolee.github.io 集成 TrendRadar

本文档是专门为 `conf-haolee.github.io` 博客集成 TrendRadar 的快速指南。

## 🎯 最简单的方式（推荐）：iframe 嵌入

只需 3 步，5 分钟内完成：

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

**Q: iframe 显示空白？**  
A: 检查 TrendRadar 的 GitHub Pages 是否已启用并正常工作。

**Q: 页面高度不够？**  
A: 调整 iframe 的 `height` 样式，例如改为 `120vh`。

**Q: 想要自定义样式？**  
A: 参考[完整文档](HEXO_INTEGRATION.md)中的方案二或方案三。

## 📚 相关资源

- [TrendRadar 项目主页](https://github.com/sansan0/TrendRadar)
- [Hexo 官方文档](https://hexo.io/zh-cn/docs/)
- [示例页面模板](hexo-page-example.md)
- [GitHub Actions 自动部署](hexo-deployment-workflow.yml)
