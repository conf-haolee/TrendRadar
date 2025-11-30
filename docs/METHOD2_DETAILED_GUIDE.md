# 方案二详细配置指南：将 TrendRadar 部署到 Hexo 博客子目录

本指南专门为 `conf-haolee.github.io` 博客详细介绍如何使用**方案二：部署到子目录**。

## 📋 方案概述

**方案二**会将 TrendRadar 的内容自动部署到您的 Hexo 博客仓库的 `source/trendradar/` 目录下，实现完全集成：

- ✅ **统一域名**：直接访问 `https://conf-haolee.github.io/trendradar/`（不是 iframe）
- ✅ **SEO 友好**：搜索引擎可以正常索引
- ✅ **自动同步**：TrendRadar 更新后自动推送到博客
- ✅ **完整集成**：看起来像博客的一部分

## 🎯 实现原理

```
TrendRadar 仓库 (爬虫运行)
    ↓
生成 index.html 和 output/
    ↓
通过 GitHub Actions 自动复制
    ↓
Hexo 博客仓库 (source/trendradar/)
    ↓
Hexo 构建并部署
    ↓
https://conf-haolee.github.io/trendradar/
```

## 📝 配置步骤

### 步骤 1：创建 GitHub Personal Access Token

这个 Token 用于让 TrendRadar 仓库有权限推送内容到您的 Hexo 博客仓库。

1. **访问 GitHub Token 创建页面**
   - 登录 GitHub
   - 访问：https://github.com/settings/tokens
   - 点击 "Generate new token" → "Generate new token (classic)"

2. **配置 Token**
   - **Note（名称）**：填写 `TrendRadar to Hexo Deployment`
   - **Expiration（有效期）**：选择 `No expiration`（或根据需要选择）
   - **Select scopes（权限）**：勾选 `repo`（完整的仓库访问权限）
     - ✅ repo (Full control of private repositories)
   
3. **生成并保存 Token**
   - 点击底部 "Generate token" 按钮
   - **重要**：复制生成的 Token（格式如：`ghp_xxxxxxxxxxxxxxxxxxxx`）
   - ⚠️ **此 Token 只显示一次，请立即保存到安全的地方**

### 步骤 2：在 TrendRadar 仓库添加 Secret

将上一步生成的 Token 添加到 TrendRadar 仓库的 Secrets 中。

1. **访问 TrendRadar 仓库的 Secrets 设置**
   - 打开：https://github.com/conf-haolee/TrendRadar/settings/secrets/actions
   - 点击 "New repository secret" 按钮

2. **添加 Secret**
   - **Name（名称）**：填写 `HEXO_REPO_TOKEN`
   - **Secret（值）**：粘贴步骤 1 中生成的 Token
   - 点击 "Add secret" 保存

### 步骤 3：修改 TrendRadar 的 GitHub Actions 工作流

编辑 TrendRadar 仓库中的 `.github/workflows/crawler.yml` 文件。

**方法 A：在 GitHub 网页上编辑**

1. 访问：https://github.com/conf-haolee/TrendRadar/blob/main/.github/workflows/crawler.yml
2. 点击右上角的铅笔图标（Edit this file）
3. 找到文件末尾的 `git push` 行（大约第 76 行）
4. 在该步骤之后添加以下内容：

```yaml
      # 新增：将生成的页面推送到 Hexo 博客仓库
      - name: Deploy to Hexo blog
        env:
          HEXO_REPO_TOKEN: ${{ secrets.HEXO_REPO_TOKEN }}
        run: |
          echo "📦 开始部署到 Hexo 博客..."
          
          # 克隆您的 Hexo 博客仓库
          git clone https://${HEXO_REPO_TOKEN}@github.com/conf-haolee/conf-haolee.github.io.git hexo-blog
          
          # 创建 trendradar 目录
          mkdir -p hexo-blog/source/trendradar
          
          # 复制 TrendRadar 生成的文件
          echo "📋 复制文件到 Hexo 博客..."
          cp index.html hexo-blog/source/trendradar/
          
          # 复制输出目录（包含历史数据）
          if [ -d "output" ]; then
            cp -r output hexo-blog/source/trendradar/
          fi
          
          # 复制图片资源
          if [ -d "_image" ]; then
            cp -r _image hexo-blog/source/trendradar/
          fi
          
          # 创建 .nojekyll 文件（确保 GitHub Pages 不忽略下划线开头的文件夹）
          touch hexo-blog/source/trendradar/.nojekyll
          
          # 提交并推送到 Hexo 博客仓库
          cd hexo-blog
          git config user.name 'TrendRadar Bot'
          git config user.email 'bot@trendradar.com'
          git add source/trendradar
          
          # 检查是否有更改
          if git diff --staged --quiet; then
            echo "ℹ️  没有新的更改"
          else
            COMMIT_TIME=$(TZ=Asia/Shanghai date '+%Y-%m-%d %H:%M:%S')
            git commit -m "🔄 Update TrendRadar: ${COMMIT_TIME}"
            git push
            echo "✅ TrendRadar 已成功部署到 Hexo 博客！"
          fi
```

5. 滚动到页面底部，填写 commit 信息
6. 选择 "Commit directly to the main branch"
7. 点击 "Commit changes" 保存

**完整的工作流位置示意：**

```yaml
      - name: Commit and push if changes
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          git add -A
          git diff --quiet && git diff --staged --quiet || (git commit -m "Auto update by GitHub Actions at $(TZ=Asia/Shanghai date)" && git push)
      
      # 👆 在上面这个步骤之后
      # 👇 添加下面的新步骤
      
      - name: Deploy to Hexo blog
        env:
          HEXO_REPO_TOKEN: ${{ secrets.HEXO_REPO_TOKEN }}
        run: |
          # ... 上面的代码 ...
```

### 步骤 4：配置 Hexo 跳过渲染

告诉 Hexo 不要处理 TrendRadar 的文件，直接复制到输出目录。

1. **编辑 Hexo 博客的配置文件**
   - 打开您的 Hexo 博客仓库：https://github.com/conf-haolee/conf-haolee.github.io
   - 找到并编辑根目录的 `_config.yml` 文件
   
2. **添加跳过渲染规则**
   
   在 `_config.yml` 文件中找到 `skip_render` 配置项（如果没有就添加）：

```yaml
# 跳过渲染 TrendRadar 文件
skip_render:
  - trendradar/**
```

   如果已经有其他 `skip_render` 配置，添加到列表中：

```yaml
skip_render:
  - README.md
  - trendradar/**  # 新增这一行
```

3. **提交更改**
   - 填写 commit 信息：`Configure Hexo to skip rendering TrendRadar files`
   - 点击 "Commit changes"

### 步骤 5：添加导航菜单（可选）

在您的 Hexo 主题配置中添加 TrendRadar 链接。

1. **找到主题配置文件**
   - 通常是 `themes/你的主题名/_config.yml`
   - 或者是根目录的 `_config.yml` 中的 `menu` 配置

2. **添加菜单项**

```yaml
menu:
  首页: /
  归档: /archives
  热点新闻: /trendradar/  # 新增这一行
  关于: /about
```

3. **提交更改**

### 步骤 6：测试部署

现在可以测试整个流程是否正常工作。

1. **手动触发 TrendRadar 爬虫**
   - 访问：https://github.com/conf-haolee/TrendRadar/actions
   - 点击 "Hot News Crawler" 工作流
   - 点击 "Run workflow" 按钮
   - 等待工作流完成（大约 2-3 分钟）

2. **检查工作流日志**
   - 点击最新的工作流运行记录
   - 查看 "Deploy to Hexo blog" 步骤的日志
   - 应该能看到 "✅ TrendRadar 已成功部署到 Hexo 博客！" 消息

3. **验证文件已推送到 Hexo 仓库**
   - 访问：https://github.com/conf-haolee/conf-haolee.github.io
   - 查看 `source/trendradar/` 目录是否存在
   - 应该能看到 `index.html`、`output/`、`_image/` 等文件

4. **等待 Hexo 部署完成**
   - Hexo 博客会自动触发部署（如果配置了自动部署）
   - 或者手动运行 Hexo 部署：
     ```bash
     cd 你的hexo博客目录
     hexo clean
     hexo generate
     hexo deploy
     ```

5. **访问最终页面**
   - 打开：https://conf-haolee.github.io/trendradar/
   - 应该能看到 TrendRadar 页面（不是 iframe，而是直接显示）

## 🎉 完成！

现在 TrendRadar 已经完全集成到您的 Hexo 博客中：

- 📍 **访问地址**：https://conf-haolee.github.io/trendradar/
- 🔄 **自动更新**：每小时自动运行并推送
- 🌐 **统一域名**：与博客其他页面使用同一域名
- 🔍 **SEO 友好**：搜索引擎可以索引

## 📊 更新机制

部署完成后，TrendRadar 会按以下方式更新：

1. **自动更新**：每小时整点运行爬虫
2. **自动推送**：爬虫完成后自动推送到 Hexo 博客仓库
3. **自动部署**：Hexo 博客自动构建和部署（如果启用了 GitHub Actions）

## 🔧 高级配置

### 自定义更新频率

修改 TrendRadar 仓库的 `.github/workflows/crawler.yml` 中的 cron 表达式：

```yaml
on:
  schedule:
    - cron: "0 * * * *"      # 每小时（默认）
    # - cron: "*/30 * * * *"  # 每30分钟
    # - cron: "0 */2 * * *"   # 每2小时
```

### 只复制最新数据（减少仓库大小）

如果不需要历史数据，可以修改部署脚本，只复制 `index.html`：

```yaml
# 只复制主页面，不复制 output 目录
cp index.html hexo-blog/source/trendradar/
# 注释掉或删除：
# if [ -d "output" ]; then
#   cp -r output hexo-blog/source/trendradar/
# fi
```

### 在不同分支部署

如果想在测试分支先验证，可以修改克隆命令：

```bash
git clone -b test-branch https://${HEXO_REPO_TOKEN}@github.com/conf-haolee/conf-haolee.github.io.git hexo-blog
```

## ❓ 常见问题

### Q1: 工作流执行失败，提示 "Authentication failed"

**A**: Token 配置有问题：
1. 检查 Secret 名称是否正确：`HEXO_REPO_TOKEN`（区分大小写）
2. 检查 Token 是否有 `repo` 权限
3. 检查 Token 是否过期
4. 重新生成 Token 并更新 Secret

### Q2: 文件推送成功，但页面显示 404

**A**: 检查以下几点：
1. 确认 Hexo 配置中添加了 `skip_render: - trendradar/**`
2. 确认 Hexo 博客已经重新构建和部署
3. 访问完整路径：`https://conf-haolee.github.io/trendradar/index.html`
4. 检查 Hexo 的 GitHub Pages 设置是否正确

### Q3: 页面样式错乱

**A**: 可能是路径问题：
1. 检查 TrendRadar 的 `index.html` 中的资源路径
2. 确保 `_image` 目录已正确复制
3. 查看浏览器控制台的错误信息

### Q4: 想回退到方案一（iframe）

**A**: 
1. 删除 TrendRadar 工作流中的 "Deploy to Hexo blog" 步骤
2. 在 Hexo 博客中删除 `source/trendradar/` 目录
3. 按照方案一的步骤重新配置

### Q5: 如何查看部署日志

**A**: 
1. 访问：https://github.com/conf-haolee/TrendRadar/actions
2. 点击最新的工作流运行记录
3. 展开 "Deploy to Hexo blog" 步骤查看详细日志

## 📚 相关资源

- [方案一：iframe 嵌入指南](QUICKSTART_conf-haolee.md)
- [完整集成文档](HEXO_INTEGRATION.md)
- [GitHub Actions 工作流示例](hexo-deployment-workflow.yml)
- [TrendRadar 主项目](https://github.com/sansan0/TrendRadar)

## 🆚 方案对比

| 特性 | 方案一（iframe） | 方案二（子目录） |
|------|-----------------|-----------------|
| 配置难度 | ⭐ 简单 | ⭐⭐ 中等 |
| 配置时间 | 5 分钟 | 15-20 分钟 |
| 域名统一 | ❌ 两个域名 | ✅ 统一域名 |
| SEO 优化 | ❌ 有限 | ✅ 友好 |
| 维护成本 | ✅ 零成本 | ⭐ 需要 Token 管理 |
| 样式控制 | ❌ 受限 | ✅ 完全控制 |
| 页面加载 | iframe 嵌套 | 直接访问 |

## 💡 建议

- **新手用户**：先使用方案一熟悉流程
- **进阶用户**：使用方案二获得更好的集成效果
- **专业用户**：可以基于方案二进一步自定义

---

如有任何问题，欢迎在 Issues 中提问！
