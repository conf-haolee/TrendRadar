# GitHub Pages 快速部署指南

> **快速参考版** - 如需完整教程和故障排查，请查看 [完整部署指南](GITHUB_PAGES_DEPLOYMENT.md)

## 🚀 最简单的部署方法（5分钟搞定）

### 步骤 1：Fork 项目

点击 [TrendRadar](https://github.com/sansan0/TrendRadar) 右上角的 **Fork** 按钮

### 步骤 2：启用 GitHub Pages

1. 进入你的仓库（`https://github.com/你的用户名/TrendRadar`）
2. 点击 **Settings** > **Pages**
3. 在 **Branch** 选择 `master` 或 `main`
4. 在 **Folder** 选择 `/ (root)`
5. 点击 **Save**

### 步骤 3：访问你的新闻网站

等待 1-3 分钟后，访问：
```
https://你的用户名.github.io/TrendRadar/
```

🎉 **完成！** 你现在有一个新闻聚合网站了！

---

## 📱 想要自动更新新闻？

### 配置文件

1. 编辑 `config/config.yaml`（参考 [README](../README.md)）
2. 创建 `config/frequency_words.txt` 添加你关心的关键词

### 设置推送通知（可选）

在仓库的 **Settings** > **Secrets** 中添加：

| 平台 | Secret Name | 说明 |
|------|-------------|------|
| 企业微信 | `WEWORK_WEBHOOK_URL` | 机器人 Webhook 地址 |
| 飞书 | `FEISHU_WEBHOOK_URL` | 机器人 Webhook 地址 |
| Telegram | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID` | Bot Token 和聊天 ID |

---

## 🔗 集成到现有 GitHub Pages 网站

### 方法 1：使用链接

在你的网站 HTML 中添加：

```html
<a href="https://你的用户名.github.io/TrendRadar/" target="_blank">
  📰 查看最新新闻
</a>
```

### 方法 2：使用 iframe 内嵌

```html
<iframe 
  src="https://你的用户名.github.io/TrendRadar/" 
  width="100%" 
  height="800px" 
  frameborder="0">
</iframe>
```

### 方法 3：使用 Git Submodule

```bash
cd 你的网站目录
git submodule add https://github.com/你的用户名/TrendRadar.git news
git add .gitmodules news
git commit -m "添加新闻聚合功能"
git push
```

访问 `https://你的用户名.github.io/news/`

---

## 🌐 使用自定义域名

### DNS 配置（子域名）

在域名服务商添加 CNAME 记录：

```
类型: CNAME
名称: news
值: 你的用户名.github.io
```

### GitHub 配置

1. 仓库 **Settings** > **Pages** > **Custom domain**
2. 填写：`news.你的域名.com`
3. 点击 **Save**
4. ✅ 勾选 **Enforce HTTPS**

### 创建 CNAME 文件

```bash
echo "news.你的域名.com" > CNAME
git add CNAME
git commit -m "添加自定义域名"
git push
```

---

## ❓ 常见问题速查

| 问题 | 解决方法 |
|------|---------|
| 404 错误 | 检查 Pages 是否启用，等待 3-5 分钟 |
| 没有新闻 | 查看 Actions 运行日志，检查配置文件 |
| 自定义域名失效 | 确保创建了 CNAME 文件 |
| 更新版本 | 复制原项目的 main.py 文件 |

---

## 📖 需要更多帮助？

- **完整教程**：[GitHub Pages 完整部署指南](GITHUB_PAGES_DEPLOYMENT.md)
- **项目文档**：[README.md](../README.md)
- **问题反馈**：[GitHub Issues](https://github.com/sansan0/TrendRadar/issues)

---

## 💡 实用技巧

### 修改页面样式

编辑 `index.html` 的 `<style>` 部分，例如修改标题背景色：

```css
.header {
    /* 原来的颜色 */
    background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%);
    
    /* 改为红色渐变 */
    background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
}
```

### 设置推送时间段

编辑 `config/config.yaml`：

```yaml
push_window:
  enable: true
  start_time: "09:00"
  end_time: "18:00"
  timezone: "Asia/Shanghai"
```

### 备份新闻数据

定期下载仓库：**Code** > **Download ZIP**，解压后在 `output` 目录找到所有数据

---

**祝你使用愉快！记得给原项目点 ⭐ Star！**
