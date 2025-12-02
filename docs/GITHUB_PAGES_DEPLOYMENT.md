# GitHub Pages 部署完整指南

本指南将详细介绍如何将 TrendRadar 新闻聚合项目部署到 GitHub Pages，包括部署到个人主页、项目页面以及与现有 GitHub Pages 项目集成的方法。

## 📋 目录

- [部署方式概览](#部署方式概览)
- [方式一：独立项目部署（推荐）](#方式一独立项目部署推荐)
- [方式二：集成到现有 GitHub Pages 项目](#方式二集成到现有-github-pages-项目)
- [方式三：使用自定义域名](#方式三使用自定义域名)
- [常见问题解答](#常见问题解答)

---

## 部署方式概览

TrendRadar 支持三种 GitHub Pages 部署方式：

| 部署方式 | 适用场景 | 访问地址示例 | 难度 |
|---------|---------|------------|------|
| **独立项目部署** | 首次使用，想快速体验 | `https://你的用户名.github.io/TrendRadar/` | ⭐ 简单 |
| **集成到现有项目** | 已有 GitHub Pages，想添加新闻聚合功能 | `https://你的用户名.github.io/news/` 或 `https://你的域名.com/news/` | ⭐⭐ 中等 |
| **自定义域名** | 想使用自己的域名访问 | `https://news.你的域名.com` | ⭐⭐⭐ 较难 |

---

## 方式一：独立项目部署（推荐）

这是最简单的部署方式，适合首次使用 TrendRadar 的用户。

### 📝 步骤说明

#### 1. Fork 本项目

1. 访问 [TrendRadar 项目主页](https://github.com/sansan0/TrendRadar)
2. 点击右上角的 **"Fork"** 按钮
3. 等待 Fork 完成，会自动跳转到你的仓库

#### 2. 启用 GitHub Pages

1. 进入你 Fork 后的仓库（地址类似：`https://github.com/你的用户名/TrendRadar`）
2. 点击仓库顶部的 **Settings**（设置）
3. 在左侧菜单中找到并点击 **Pages**
4. 在 **"Build and deployment"** 部分：
   - **Source（源）**：选择 **"Deploy from a branch"**
   - **Branch（分支）**：选择 **"master"** 或 **"main"** 分支
   - **Folder（文件夹）**：选择 **"/ (root)"**
5. 点击 **Save**（保存）

#### 3. 等待部署完成

1. GitHub 会自动开始部署，通常需要 1-3 分钟
2. 刷新页面，会在顶部看到访问地址：
   ```
   Your site is live at https://你的用户名.github.io/TrendRadar/
   ```
3. 点击链接即可访问你的新闻聚合页面

#### 4. 配置新闻抓取（可选）

如果想要自动更新新闻内容，需要配置 GitHub Actions：

1. 参考主 [README.md](../README.md) 的 **"🚀 快速开始"** 部分
2. 配置 `config/config.yaml` 和 `config/frequency_words.txt`
3. 设置 GitHub Secrets（如需推送通知）
4. GitHub Actions 会自动运行，并将生成的新闻内容推送到仓库

### ✅ 完成！

现在你可以通过 `https://你的用户名.github.io/TrendRadar/` 访问你的新闻聚合网页了。

---

## 方式二：集成到现有 GitHub Pages 项目

如果你已经有一个 GitHub Pages 项目（例如个人博客、作品集等），可以将 TrendRadar 作为其中的一个子目录集成进去。

### 📝 场景说明

**示例场景：**
- 你的现有 GitHub Pages 项目：`https://你的用户名.github.io/` （或使用自定义域名）
- 你想添加新闻聚合功能到：`https://你的用户名.github.io/news/`

### 🔧 方法 A：使用 Git Submodule（推荐）

这种方法可以保持 TrendRadar 项目独立，方便后续更新。

#### 步骤：

1. **在本地克隆你的现有 GitHub Pages 项目**

   ```bash
   git clone https://github.com/你的用户名/你的用户名.github.io.git
   cd 你的用户名.github.io
   ```

2. **Fork TrendRadar 到你的账号**
   
   先在 GitHub 上 Fork [TrendRadar 项目](https://github.com/sansan0/TrendRadar)

3. **添加 TrendRadar 作为 Submodule**

   ```bash
   # 将 TrendRadar 添加到 news 子目录
   git submodule add https://github.com/你的用户名/TrendRadar.git news
   
   # 提交更改
   git add .gitmodules news
   git commit -m "添加 TrendRadar 新闻聚合功能"
   git push
   ```

4. **配置 TrendRadar**

   ```bash
   cd news
   # 配置 config/config.yaml 和 config/frequency_words.txt
   # 参考主 README.md 进行配置
   ```

5. **在 TrendRadar 仓库中启用 GitHub Actions**

   - 进入你 Fork 的 TrendRadar 仓库
   - 配置 GitHub Secrets（参考主 README.md）
   - GitHub Actions 会自动运行并生成新闻内容

6. **更新 Submodule 并同步到主项目**

   ```bash
   # 在主项目目录
   cd 你的用户名.github.io
   
   # 更新 submodule 到最新内容
   git submodule update --remote news
   
   # 提交更新
   git add news
   git commit -m "更新新闻内容"
   git push
   ```

#### 自动化更新（可选）：

你可以创建一个 GitHub Action 来自动同步 TrendRadar 的更新：

在你的主项目中创建 `.github/workflows/sync-news.yml`：

```yaml
name: Sync News from TrendRadar

on:
  schedule:
    - cron: '0 */6 * * *'  # 每6小时运行一次（0:00, 6:00, 12:00, 18:00，共4次）
  workflow_dispatch:  # 允许手动触发

permissions:
  contents: write

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
        with:
          submodules: true
          
      - name: Update submodule
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          git submodule update --remote --merge news
          
      - name: Commit and push if changes
        run: |
          git add news
          git diff --quiet && git diff --staged --quiet || (git commit -m "Auto sync news from TrendRadar" && git push)
```

### 🔧 方法 B：手动复制文件

如果你不想使用 Submodule，可以直接复制文件。

#### 步骤：

1. **下载 TrendRadar 的 output 文件夹和 index.html**

   ```bash
   # 克隆 TrendRadar 项目
   git clone https://github.com/sansan0/TrendRadar.git temp-trendradar
   
   # 复制必要文件到你的项目
   cd 你的用户名.github.io
   mkdir -p news
   cp temp-trendradar/index.html news/
   cp -r temp-trendradar/output news/
   cp -r temp-trendradar/_image news/
   
   # 删除临时目录
   rm -rf temp-trendradar
   ```

2. **修改 index.html 中的路径（重要！）**

   编辑 `news/index.html`，将所有路径改为相对路径：
   
   ```html
   <!-- 修改前 -->
   <img src="/_image/banner.webp" ...>
   
   <!-- 修改后 -->
   <img src="../_image/banner.webp" ...>
   ```
   
   或者将 `_image` 目录也复制到 `news/` 目录内。

3. **提交到仓库**

   ```bash
   git add news
   git commit -m "添加新闻聚合功能"
   git push
   ```

4. **访问新闻页面**

   访问 `https://你的用户名.github.io/news/` 即可看到新闻聚合页面。

#### 更新新闻内容：

手动方式需要定期从 TrendRadar 仓库同步更新：

```bash
# 定期执行这些命令更新新闻
cd 你的用户名.github.io
git pull origin main  # 更新你的主项目
cd ../temp-trendradar
git pull origin master  # 更新 TrendRadar
cp -r output ../你的用户名.github.io/news/
cd ../你的用户名.github.io
git add news/output
git commit -m "更新新闻内容"
git push
```

### 🔧 方法 C：使用独立仓库 + 重定向

保持 TrendRadar 为独立仓库，在主页面添加链接或内嵌 iframe。

#### 步骤：

1. **按照 [方式一](#方式一独立项目部署推荐) 部署 TrendRadar**

2. **在你的主页面添加链接**

   编辑你的主页面 HTML：
   
   ```html
   <a href="https://你的用户名.github.io/TrendRadar/" target="_blank">
     📰 查看最新新闻聚合
   </a>
   ```

3. **或者使用 iframe 内嵌（可选）**

   ```html
   <iframe 
     src="https://你的用户名.github.io/TrendRadar/" 
     width="100%" 
     height="800px" 
     frameborder="0">
   </iframe>
   ```

---

## 方式三：使用自定义域名

如果你有自己的域名，可以配置 GitHub Pages 使用自定义域名访问 TrendRadar。

### 📝 步骤说明

#### 1. 在域名服务商配置 DNS

##### 方案 A：使用子域名（推荐）

例如：`news.你的域名.com`

在域名服务商（如阿里云、腾讯云、Cloudflare）添加 CNAME 记录：

```
类型: CNAME
名称: news
值: 你的用户名.github.io
TTL: 3600（或默认值）
```

##### 方案 B：使用根域名

例如：`你的域名.com`

添加 A 记录指向 GitHub Pages 的 IP：

```
类型: A
名称: @
值: 185.199.108.153
TTL: 3600

类型: A
名称: @
值: 185.199.109.153
TTL: 3600

类型: A
名称: @
值: 185.199.110.153
TTL: 3600

类型: A
名称: @
值: 185.199.111.153
TTL: 3600
```

#### 2. 在 GitHub 仓库配置自定义域名

1. 进入你的 TrendRadar 仓库
2. 点击 **Settings** > **Pages**
3. 在 **"Custom domain"** 输入框中填写你的域名（例如：`news.你的域名.com`）
4. 点击 **Save**
5. 等待 DNS 检查完成（可能需要几分钟到几小时）
6. ✅ 勾选 **"Enforce HTTPS"**（强制使用 HTTPS）

#### 3. 创建 CNAME 文件（重要！）

在仓库根目录创建 `CNAME` 文件，内容为你的域名：

```bash
# 在本地仓库
cd TrendRadar
echo "news.你的域名.com" > CNAME
git add CNAME
git commit -m "添加自定义域名"
git push
```

**注意**：每次 GitHub Actions 运行时，如果没有 CNAME 文件，自定义域名配置会被清除。

#### 4. 验证部署

等待几分钟后，访问你的自定义域名（例如：`https://news.你的域名.com`），应该能看到 TrendRadar 页面。

---

## 常见问题解答

### Q1: GitHub Pages 页面显示 404 错误？

**原因：**
- GitHub Pages 还未启用或部署未完成
- 分支或文件夹选择错误
- 访问路径不正确

**解决方法：**
1. 检查 **Settings** > **Pages** 是否已正确配置
2. 确认分支选择为 `master` 或 `main`，文件夹为 `/ (root)`
3. 等待 3-5 分钟让 GitHub Pages 部署完成
4. 检查访问的 URL 是否正确（注意仓库名大小写）

### Q2: 页面显示但没有新闻内容？

**原因：**
- GitHub Actions 还未运行
- 配置文件不正确
- `output` 目录为空

**解决方法：**
1. 检查 `config/config.yaml` 和 `config/frequency_words.txt` 是否配置正确
2. 在仓库的 **Actions** 标签页手动触发工作流
3. 查看 Actions 运行日志，检查是否有错误
4. 确保 `output` 目录中有新闻数据文件

### Q3: 如何更新 TrendRadar 到最新版本？

**方法：**

⚠️ **不建议使用 GitHub 的 "Sync fork" 功能**，原因如下：
- TrendRadar 采用特殊的升级方式，只需更新特定文件（如 `main.py`）
- Sync fork 会同步所有更改，可能导致配置文件冲突
- 可能覆盖你的个性化配置（如 `config.yaml`、`frequency_words.txt`）

**推荐的升级步骤：**

1. 访问 [原项目](https://github.com/sansan0/TrendRadar) 查看更新日志
2. 根据版本说明，复制需要更新的文件（通常是 `main.py`）
3. 在你的仓库中更新对应文件
4. 提交更改

**示例（小版本升级）：**

```bash
# 1. 从原项目复制最新的 main.py
# 2. 在你的仓库中替换该文件
git add main.py
git commit -m "升级到 v3.2.0"
git push
```

### Q4: 自定义域名配置后总是失效？

**原因：**
- 没有创建 CNAME 文件
- GitHub Actions 运行时覆盖了 CNAME 文件

**解决方法：**
1. 在仓库根目录创建 `CNAME` 文件，内容为你的域名
2. 确保 `.github/workflows/crawler.yml` 中的 `git add -A` 不会删除 CNAME
3. 可以在 workflow 中添加保护：
   ```yaml
   - name: Commit and push if changes
     run: |
       git config --global user.name 'GitHub Actions'
       git config --global user.email 'actions@github.com'
       
       # 保存 CNAME 文件
       if [ -f CNAME ]; then
         cp CNAME CNAME.backup
       fi
       
       git add -A
       
       # 恢复 CNAME 文件
       if [ -f CNAME.backup ]; then
         mv CNAME.backup CNAME
         git add CNAME
       fi
       
       git diff --quiet && git diff --staged --quiet || (git commit -m "Auto update by GitHub Actions at $(TZ=Asia/Shanghai date)" && git push)
   ```

### Q5: 如何在现有博客中内嵌 TrendRadar 页面？

**方法 1：使用 iframe**

```html
<div style="width: 100%; height: 800px;">
  <iframe 
    src="https://你的用户名.github.io/TrendRadar/" 
    width="100%" 
    height="100%" 
    frameborder="0"
    style="border: none;">
  </iframe>
</div>
```

**方法 2：使用链接**

```html
<a href="https://你的用户名.github.io/TrendRadar/" 
   target="_blank" 
   class="news-link">
  📰 查看最新热点新闻
</a>
```

**方法 3：在 Jekyll 博客中集成**

如果你使用 Jekyll 博客，可以在 `_includes` 中创建组件：

```liquid
<!-- _includes/trendradar.html -->
<div class="trendradar-widget">
  <h2>📰 最新热点</h2>
  <iframe 
    src="https://你的用户名.github.io/TrendRadar/" 
    width="100%" 
    height="600px" 
    frameborder="0">
  </iframe>
</div>
```

然后在文章或页面中引用：

```liquid
{% include trendradar.html %}
```

### Q6: 如何修改页面样式？

TrendRadar 的页面样式定义在 `index.html` 文件的 `<style>` 标签中。

**修改步骤：**

1. 编辑仓库中的 `index.html` 文件
2. 在 `<style>` 标签内修改 CSS 样式
3. 提交更改：
   ```bash
   git add index.html
   git commit -m "自定义页面样式"
   git push
   ```

**示例 - 修改标题颜色：**

```css
/* 在 index.html 的 <style> 标签中查找并修改 */
.header {
    background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%);
    /* 改为你喜欢的颜色 */
    background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
}
```

**提示**：建议使用 CSS 变量来管理主题色，这样可以更方便地统一修改：

```css
:root {
    --primary-color-start: #4f46e5;
    --primary-color-end: #7c3aed;
}

.header {
    background: linear-gradient(135deg, var(--primary-color-start) 0%, var(--primary-color-end) 100%);
}
```

### Q7: 如何只显示特定时间段的新闻？

修改 `config/config.yaml` 中的推送时间窗口配置：

```yaml
push_window:
  enable: false  # 改为 true 启用时间窗口
  start_time: "09:00"  # 开始时间
  end_time: "18:00"    # 结束时间
  timezone: "Asia/Shanghai"  # 时区
  push_once: true  # 每天只推送一次
```

这样 GitHub Actions 只会在指定时间段内生成新闻。

### Q8: 如何设置访问密码保护？

GitHub Pages 本身不支持密码保护，但你可以：

**方法 1：使用私有仓库**
- 将仓库设置为 Private（需要 GitHub Pro）
- 只有你和授权的协作者可以访问

**方法 2：使用第三方服务**
- 使用 Cloudflare 的 Access 功能添加身份验证
- 使用 Vercel 或 Netlify 部署，它们支持密码保护

**方法 3：在代码中添加简单验证**

在 `index.html` 的 `<head>` 标签后添加：

```html
<script>
    // 简单的密码保护（不安全，仅供演示）
    const password = prompt('请输入访问密码:');
    if (password !== '你的密码') {
        document.body.innerHTML = '<h1>密码错误</h1>';
        throw new Error('密码错误');
    }
</script>
```

**注意**：此方法不安全，仅能防止普通访客，技术人员可以轻易绕过。

### Q9: GitHub Actions 运行失败，如何调试？

**检查步骤：**

1. **查看运行日志**
   - 进入仓库的 **Actions** 标签
   - 点击失败的工作流运行
   - 展开每个步骤查看错误信息

2. **常见错误及解决方法**

   | 错误信息 | 原因 | 解决方法 |
   |---------|------|---------|
   | `config/config.yaml 文件不存在` | 配置文件缺失 | 参考 README 创建配置文件 |
   | `frequency_words.txt 文件不存在` | 关键词文件缺失 | 创建 `config/frequency_words.txt` |
   | `Permission denied` | 权限不足 | 检查仓库 Settings > Actions > Workflow permissions 是否为 "Read and write" |
   | API 请求失败 | 网络问题或 API 限流 | 等待一段时间后重试 |

3. **启用调试日志**
   
   在仓库 Settings > Secrets 中添加：
   - Name: `ACTIONS_RUNNER_DEBUG`
   - Value: `true`

### Q10: 如何备份新闻数据？

**方法 1：自动备份到另一个仓库**

创建 `.github/workflows/backup.yml`：

```yaml
name: Backup News Data

on:
  schedule:
    - cron: '0 0 * * 0'  # 每周日午夜备份
  workflow_dispatch:

jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        
      - name: Create backup
        run: |
          tar -czf news-backup-$(date +%Y%m%d).tar.gz output/
          
      - name: Upload to Release
        uses: softprops/action-gh-release@v1
        with:
          files: news-backup-*.tar.gz
          tag_name: backup-$(date +%Y%m%d)
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**方法 2：手动下载**

1. 访问你的仓库
2. 点击绿色的 **Code** 按钮
3. 选择 **Download ZIP**
4. 解压后在 `output` 目录找到所有历史数据

---

## 📞 获取帮助

如果遇到问题：

1. **查看主文档**：[README.md](../README.md)
2. **搜索 Issues**：[GitHub Issues](https://github.com/sansan0/TrendRadar/issues)
3. **提交新 Issue**：详细描述你的问题、环境、错误日志
4. **关注公众号**：查看主 README 中的联系方式

---

## 🎉 部署成功！

恭喜你完成 TrendRadar 的 GitHub Pages 部署！现在你可以：

- ✅ 通过网页随时查看热点新闻
- ✅ 分享链接给朋友和同事
- ✅ 保存为图片分享到社交媒体
- ✅ 在移动设备上随时访问

**提示**：记得给原项目点个 ⭐ Star 支持作者！
