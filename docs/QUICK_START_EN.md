# GitHub Pages Quick Deployment Guide

> **Quick Reference** - For complete tutorial and troubleshooting, see [Full Deployment Guide](GITHUB_PAGES_DEPLOYMENT_EN.md)

## 🚀 Simplest Deployment (5 Minutes)

### Step 1: Fork the Project

Click the **Fork** button on [TrendRadar](https://github.com/sansan0/TrendRadar)

### Step 2: Enable GitHub Pages

1. Go to your repository (`https://github.com/yourusername/TrendRadar`)
2. Click **Settings** > **Pages**
3. Under **Branch**, select `master` or `main`
4. Under **Folder**, select `/ (root)`
5. Click **Save**

### Step 3: Access Your News Site

After 1-3 minutes, visit:
```
https://yourusername.github.io/TrendRadar/
```

🎉 **Done!** You now have a news aggregation website!

---

## 📱 Want Automatic News Updates?

### Configuration Files

1. Edit `config/config.yaml` (refer to [README](../README-EN.md))
2. Create `config/frequency_words.txt` with your keywords of interest

### Set Up Push Notifications (Optional)

Add in **Settings** > **Secrets**:

| Platform | Secret Name | Description |
|----------|-------------|-------------|
| WeWork | `WEWORK_WEBHOOK_URL` | Robot Webhook URL |
| Feishu | `FEISHU_WEBHOOK_URL` | Robot Webhook URL |
| Telegram | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID` | Bot Token and Chat ID |

---

## 🔗 Integration with Existing GitHub Pages Site

### Method 1: Using Link

Add to your website HTML:

```html
<a href="https://yourusername.github.io/TrendRadar/" target="_blank">
  📰 View Latest News
</a>
```

### Method 2: Using iframe Embed

```html
<iframe 
  src="https://yourusername.github.io/TrendRadar/" 
  width="100%" 
  height="800px" 
  frameborder="0">
</iframe>
```

### Method 3: Using Git Submodule

```bash
cd your-site-directory
git submodule add https://github.com/yourusername/TrendRadar.git news
git add .gitmodules news
git commit -m "Add news aggregation feature"
git push
```

Access at `https://yourusername.github.io/news/`

---

## 🌐 Using Custom Domain

### DNS Configuration (Subdomain)

Add CNAME record at your domain provider:

```
Type: CNAME
Name: news
Value: yourusername.github.io
```

### GitHub Configuration

1. Repository **Settings** > **Pages** > **Custom domain**
2. Enter: `news.yourdomain.com`
3. Click **Save**
4. ✅ Check **Enforce HTTPS**

### Create CNAME File

```bash
echo "news.yourdomain.com" > CNAME
git add CNAME
git commit -m "Add custom domain"
git push
```

---

## ❓ Quick Troubleshooting

| Issue | Solution |
|-------|----------|
| 404 Error | Check Pages is enabled, wait 3-5 minutes |
| No News | Check Actions logs, verify config files |
| Custom Domain Fails | Ensure CNAME file exists |
| Update Version | Copy main.py from original project |

---

## 📖 Need More Help?

- **Full Tutorial**: [Complete GitHub Pages Deployment Guide](GITHUB_PAGES_DEPLOYMENT_EN.md)
- **Project Docs**: [README-EN.md](../README-EN.md)
- **Issue Report**: [GitHub Issues](https://github.com/sansan0/TrendRadar/issues)

---

## 💡 Useful Tips

### Modify Page Styles

Edit `<style>` section in `index.html`, e.g., change header background:

```css
.header {
    /* Original color */
    background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%);
    
    /* Change to red gradient */
    background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
}
```

### Set Push Time Window

Edit `config/config.yaml`:

```yaml
push_window:
  enable: true
  start_time: "09:00"
  end_time: "18:00"
  timezone: "Asia/Shanghai"
```

### Backup News Data

Periodically download repository: **Code** > **Download ZIP**, find all data in `output` directory

---

**Enjoy! Remember to give the original project a ⭐ Star!**
