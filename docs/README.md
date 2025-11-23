# TrendRadar 集成文档

本目录包含 TrendRadar 与其他平台集成的文档和示例文件。

## 📚 文档列表

### Hexo 博客集成

| 文件 | 说明 | 语言 |
|------|------|------|
| [HEXO_INTEGRATION.md](HEXO_INTEGRATION.md) | 完整的 Hexo 集成指南，包含 3 种方案 | 中文 |
| [HEXO_INTEGRATION_EN.md](HEXO_INTEGRATION_EN.md) | Hexo Integration Guide (English version) | English |
| [QUICKSTART_conf-haolee.md](QUICKSTART_conf-haolee.md) | 针对 conf-haolee.github.io 的快速开始指南 | 中文 |
| [hexo-page-example.md](hexo-page-example.md) | Hexo 页面模板示例（可直接使用） | 中文 |
| [hexo-deployment-workflow.yml](hexo-deployment-workflow.yml) | GitHub Actions 自动部署工作流示例 | YAML |

## 🎯 快速选择指南

### 如果你想...

**5 分钟快速集成 TrendRadar 到 Hexo 博客**
→ 查看 [QUICKSTART_conf-haolee.md](QUICKSTART_conf-haolee.md)（针对 conf-haolee.github.io）  
→ 或 [HEXO_INTEGRATION.md](HEXO_INTEGRATION.md) 的方案一

**深度集成，SEO 优化**
→ 查看 [HEXO_INTEGRATION.md](HEXO_INTEGRATION.md) 的方案二

**完全自定义样式和功能**
→ 查看 [HEXO_INTEGRATION.md](HEXO_INTEGRATION.md) 的方案三

**直接使用现成的模板**
→ 复制 [hexo-page-example.md](hexo-page-example.md) 的内容

**自动化部署到 Hexo**
→ 参考 [hexo-deployment-workflow.yml](hexo-deployment-workflow.yml)

## 📖 详细说明

### 1. 完整集成指南

**HEXO_INTEGRATION.md** 提供了三种集成方案：

- **方案一：iframe 嵌入**（⭐ 简单）
  - 优点：配置简单，5 分钟完成，维护方便
  - 缺点：iframe 限制，SEO 优化有限
  - 适合：快速集成，不需要深度定制

- **方案二：部署到子目录**（⭐⭐ 中等）
  - 优点：完全集成，SEO 友好，统一域名
  - 缺点：配置相对复杂，需要 GitHub Token
  - 适合：专业用户，希望深度集成

- **方案三：自定义页面**（⭐⭐⭐ 复杂）
  - 优点：完全控制，可深度定制
  - 缺点：实现复杂，维护成本高
  - 适合：开发者，需要自定义功能

### 2. 快速开始指南

**QUICKSTART_conf-haolee.md** 是为 `conf-haolee.github.io` 定制的快速指南，包含：
- 最简单的 iframe 嵌入步骤
- 已配置好的代码（直接复制使用）
- 常见问题解答

### 3. 示例文件

**hexo-page-example.md** 是一个增强版的 Hexo 页面模板，包含：
- 加载动画
- 错误处理
- 超时重试
- 响应式优化
- 详细的使用说明

**hexo-deployment-workflow.yml** 是 GitHub Actions 工作流示例，可实现：
- TrendRadar 自动更新时推送到 Hexo 博客
- 自动化部署流程
- 支持完整或部分文件同步

## 🚀 使用流程

### 新手用户（推荐）

1. 阅读 [QUICKSTART_conf-haolee.md](QUICKSTART_conf-haolee.md)
2. 按照步骤操作（5 分钟完成）
3. 如需优化，参考 [hexo-page-example.md](hexo-page-example.md)

### 进阶用户

1. 阅读 [HEXO_INTEGRATION.md](HEXO_INTEGRATION.md) 了解所有方案
2. 根据需求选择合适的方案
3. 参考相应的示例文件实施

### 开发者

1. 阅读完整文档了解架构
2. 参考 [hexo-deployment-workflow.yml](hexo-deployment-workflow.yml) 自定义部署流程
3. 根据需要修改和扩展功能

## 🌍 语言版本

- 中文文档：HEXO_INTEGRATION.md, QUICKSTART_conf-haolee.md, hexo-page-example.md
- English Documentation: HEXO_INTEGRATION_EN.md

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来改进文档！

如果您有其他平台（如 Jekyll、Hugo、WordPress）的集成需求或方案，欢迎分享。

## 📞 获取帮助

- 查看主项目的 [FAQ](https://github.com/sansan0/TrendRadar#问题答疑与交流)
- 提交 [Issue](https://github.com/sansan0/TrendRadar/issues)
- 参考 Hexo 社区讨论

## 📝 更新日志

- 2025-11-23: 创建 Hexo 集成文档和示例文件
  - 添加完整的中英文集成指南
  - 提供可直接使用的模板和工作流
  - 创建快速开始指南

---

**返回:** [TrendRadar 主页](../README.md)
