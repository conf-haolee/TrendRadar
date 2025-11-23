# Complete GitHub Pages Deployment Guide

This guide provides detailed instructions on how to deploy the TrendRadar news aggregation project to GitHub Pages, including standalone deployment, integration with existing GitHub Pages projects, and custom domain configuration.

## 📋 Table of Contents

- [Deployment Options Overview](#deployment-options-overview)
- [Option 1: Standalone Project Deployment (Recommended)](#option-1-standalone-project-deployment-recommended)
- [Option 2: Integration with Existing GitHub Pages Project](#option-2-integration-with-existing-github-pages-project)
- [Option 3: Using Custom Domain](#option-3-using-custom-domain)
- [Frequently Asked Questions](#frequently-asked-questions)

---

## Deployment Options Overview

TrendRadar supports three GitHub Pages deployment methods:

| Deployment Method | Use Case | Access URL Example | Difficulty |
|------------------|----------|-------------------|-----------|
| **Standalone Deployment** | First-time users, quick start | `https://yourusername.github.io/TrendRadar/` | ⭐ Easy |
| **Integration** | Existing GitHub Pages, adding news feature | `https://yourusername.github.io/news/` | ⭐⭐ Medium |
| **Custom Domain** | Using your own domain | `https://news.yourdomain.com` | ⭐⭐⭐ Advanced |

---

## Option 1: Standalone Project Deployment (Recommended)

This is the simplest deployment method, suitable for first-time TrendRadar users.

### 📝 Step-by-Step Guide

#### 1. Fork the Project

1. Visit the [TrendRadar project homepage](https://github.com/sansan0/TrendRadar)
2. Click the **"Fork"** button in the top right corner
3. Wait for the fork to complete, you'll be redirected to your repository

#### 2. Enable GitHub Pages

1. Go to your forked repository (URL like: `https://github.com/yourusername/TrendRadar`)
2. Click **Settings** at the top of the repository
3. Find and click **Pages** in the left sidebar
4. In the **"Build and deployment"** section:
   - **Source**: Select **"Deploy from a branch"**
   - **Branch**: Select **"master"** or **"main"** branch
   - **Folder**: Select **"/ (root)"**
5. Click **Save**

#### 3. Wait for Deployment

1. GitHub will automatically start deployment, usually takes 1-3 minutes
2. Refresh the page, you'll see the access URL at the top:
   ```
   Your site is live at https://yourusername.github.io/TrendRadar/
   ```
3. Click the link to access your news aggregation page

#### 4. Configure News Crawling (Optional)

To enable automatic news updates, configure GitHub Actions:

1. Refer to the **"🚀 Quick Start"** section in the main [README.md](../README.md)
2. Configure `config/config.yaml` and `config/frequency_words.txt`
3. Set up GitHub Secrets (if you need notifications)
4. GitHub Actions will run automatically and push generated news content to the repository

### ✅ Done!

You can now access your news aggregation page at `https://yourusername.github.io/TrendRadar/`.

---

## Option 2: Integration with Existing GitHub Pages Project

If you already have a GitHub Pages project (e.g., personal blog, portfolio), you can integrate TrendRadar as a subdirectory.

### 📝 Scenario Description

**Example Scenario:**
- Your existing GitHub Pages: `https://yourusername.github.io/` (or custom domain)
- You want to add news at: `https://yourusername.github.io/news/`

### 🔧 Method A: Using Git Submodule (Recommended)

This method keeps TrendRadar independent, making future updates easier.

#### Steps:

1. **Clone your existing GitHub Pages project locally**

   ```bash
   git clone https://github.com/yourusername/yourusername.github.io.git
   cd yourusername.github.io
   ```

2. **Fork TrendRadar to your account**
   
   First, fork the [TrendRadar project](https://github.com/sansan0/TrendRadar) on GitHub

3. **Add TrendRadar as a Submodule**

   ```bash
   # Add TrendRadar to the news subdirectory
   git submodule add https://github.com/yourusername/TrendRadar.git news
   
   # Commit changes
   git add .gitmodules news
   git commit -m "Add TrendRadar news aggregation feature"
   git push
   ```

4. **Configure TrendRadar**

   ```bash
   cd news
   # Configure config/config.yaml and config/frequency_words.txt
   # Refer to main README.md for configuration
   ```

5. **Enable GitHub Actions in TrendRadar repository**

   - Go to your forked TrendRadar repository
   - Configure GitHub Secrets (refer to main README.md)
   - GitHub Actions will run automatically and generate news content

6. **Update Submodule and sync to main project**

   ```bash
   # In main project directory
   cd yourusername.github.io
   
   # Update submodule to latest content
   git submodule update --remote news
   
   # Commit updates
   git add news
   git commit -m "Update news content"
   git push
   ```

#### Automated Updates (Optional):

Create a GitHub Action to automatically sync TrendRadar updates:

Create `.github/workflows/sync-news.yml` in your main project:

```yaml
name: Sync News from TrendRadar

on:
  schedule:
    - cron: '0 */6 * * *'  # Check every 6 hours
  workflow_dispatch:  # Allow manual trigger

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

### 🔧 Method B: Manual File Copy

If you don't want to use Submodule, you can directly copy files.

#### Steps:

1. **Download TrendRadar's output folder and index.html**

   ```bash
   # Clone TrendRadar project
   git clone https://github.com/sansan0/TrendRadar.git temp-trendradar
   
   # Copy necessary files to your project
   cd yourusername.github.io
   mkdir -p news
   cp temp-trendradar/index.html news/
   cp -r temp-trendradar/output news/
   cp -r temp-trendradar/_image news/
   
   # Remove temporary directory
   rm -rf temp-trendradar
   ```

2. **Modify paths in index.html (Important!)**

   Edit `news/index.html`, change all paths to relative paths:
   
   ```html
   <!-- Before -->
   <img src="/_image/banner.webp" ...>
   
   <!-- After -->
   <img src="../_image/banner.webp" ...>
   ```
   
   Or copy the `_image` directory into the `news/` directory.

3. **Commit to repository**

   ```bash
   git add news
   git commit -m "Add news aggregation feature"
   git push
   ```

4. **Access news page**

   Visit `https://yourusername.github.io/news/` to see your news aggregation page.

### 🔧 Method C: Separate Repository + Link/Embed

Keep TrendRadar as a separate repository, add links or embed with iframe on your main page.

#### Steps:

1. **Deploy TrendRadar following [Option 1](#option-1-standalone-project-deployment-recommended)**

2. **Add link on your main page**

   Edit your main page HTML:
   
   ```html
   <a href="https://yourusername.github.io/TrendRadar/" target="_blank">
     📰 View Latest News Aggregation
   </a>
   ```

3. **Or use iframe embedding (Optional)**

   ```html
   <iframe 
     src="https://yourusername.github.io/TrendRadar/" 
     width="100%" 
     height="800px" 
     frameborder="0">
   </iframe>
   ```

---

## Option 3: Using Custom Domain

If you have your own domain, you can configure GitHub Pages to use a custom domain for TrendRadar.

### 📝 Step-by-Step Guide

#### 1. Configure DNS at Domain Provider

##### Option A: Using Subdomain (Recommended)

Example: `news.yourdomain.com`

Add a CNAME record at your domain provider (e.g., Cloudflare, Namecheap):

```
Type: CNAME
Name: news
Value: yourusername.github.io
TTL: 3600 (or default)
```

##### Option B: Using Root Domain

Example: `yourdomain.com`

Add A records pointing to GitHub Pages IPs:

```
Type: A
Name: @
Value: 185.199.108.153
TTL: 3600

Type: A
Name: @
Value: 185.199.109.153
TTL: 3600

Type: A
Name: @
Value: 185.199.110.153
TTL: 3600

Type: A
Name: @
Value: 185.199.111.153
TTL: 3600
```

#### 2. Configure Custom Domain in GitHub Repository

1. Go to your TrendRadar repository
2. Click **Settings** > **Pages**
3. In the **"Custom domain"** field, enter your domain (e.g., `news.yourdomain.com`)
4. Click **Save**
5. Wait for DNS check to complete (may take minutes to hours)
6. ✅ Check **"Enforce HTTPS"**

#### 3. Create CNAME File (Important!)

Create a `CNAME` file in the repository root with your domain:

```bash
# In local repository
cd TrendRadar
echo "news.yourdomain.com" > CNAME
git add CNAME
git commit -m "Add custom domain"
git push
```

**Note**: If GitHub Actions runs without a CNAME file, the custom domain configuration will be cleared.

#### 4. Verify Deployment

After a few minutes, visit your custom domain (e.g., `https://news.yourdomain.com`), you should see the TrendRadar page.

---

## Frequently Asked Questions

### Q1: GitHub Pages showing 404 error?

**Causes:**
- GitHub Pages not enabled or deployment incomplete
- Wrong branch or folder selection
- Incorrect access path

**Solutions:**
1. Check **Settings** > **Pages** is correctly configured
2. Confirm branch is `master` or `main`, folder is `/ (root)`
3. Wait 3-5 minutes for GitHub Pages to deploy
4. Check URL is correct (note repository name case)

### Q2: Page displays but no news content?

**Causes:**
- GitHub Actions hasn't run yet
- Incorrect configuration files
- Empty `output` directory

**Solutions:**
1. Check `config/config.yaml` and `config/frequency_words.txt` are correctly configured
2. Manually trigger workflow in repository's **Actions** tab
3. View Actions run logs for errors
4. Ensure `output` directory contains news data files

### Q3: How to update TrendRadar to latest version?

**Method:**

**Don't use GitHub's "Sync fork" feature**, manually update files instead:

1. Visit [original project](https://github.com/sansan0/TrendRadar) to check changelog
2. Copy files that need updating (usually `main.py`) according to version notes
3. Update corresponding files in your repository
4. Commit changes

### Q4: Custom domain configuration keeps failing?

**Causes:**
- No CNAME file created
- GitHub Actions overwrites CNAME file

**Solutions:**
1. Create `CNAME` file in repository root with your domain
2. Ensure `.github/workflows/crawler.yml`'s `git add -A` doesn't delete CNAME
3. Add protection in workflow:
   ```yaml
   - name: Commit and push if changes
     run: |
       git config --global user.name 'GitHub Actions'
       git config --global user.email 'actions@github.com'
       
       # Save CNAME file
       if [ -f CNAME ]; then
         cp CNAME CNAME.backup
       fi
       
       git add -A
       
       # Restore CNAME file
       if [ -f CNAME.backup ]; then
         mv CNAME.backup CNAME
         git add CNAME
       fi
       
       git diff --quiet && git diff --staged --quiet || (git commit -m "Auto update by GitHub Actions at $(TZ=Asia/Shanghai date)" && git push)
   ```

### Q5: How to embed TrendRadar in existing blog?

**Method 1: Using iframe**

```html
<div style="width: 100%; height: 800px;">
  <iframe 
    src="https://yourusername.github.io/TrendRadar/" 
    width="100%" 
    height="100%" 
    frameborder="0"
    style="border: none;">
  </iframe>
</div>
```

**Method 2: Using link**

```html
<a href="https://yourusername.github.io/TrendRadar/" 
   target="_blank" 
   class="news-link">
  📰 View Latest News
</a>
```

**Method 3: Jekyll blog integration**

If using Jekyll, create a component in `_includes`:

```liquid
<!-- _includes/trendradar.html -->
<div class="trendradar-widget">
  <h2>📰 Latest News</h2>
  <iframe 
    src="https://yourusername.github.io/TrendRadar/" 
    width="100%" 
    height="600px" 
    frameborder="0">
  </iframe>
</div>
```

Then include in posts or pages:

```liquid
{% include trendradar.html %}
```

### Q6: How to modify page styles?

TrendRadar's page styles are defined in the `<style>` tag of `index.html`.

**Steps:**

1. Edit `index.html` in repository
2. Modify CSS styles in `<style>` tag
3. Commit changes:
   ```bash
   git add index.html
   git commit -m "Customize page styles"
   git push
   ```

**Example - Change header color:**

```css
/* Find and modify in index.html's <style> tag */
.header {
    background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%);
    /* Change to your preferred color */
    background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
}
```

### Q7: How to display news only during specific hours?

Modify push window configuration in `config/config.yaml`:

```yaml
push_window:
  enable: false  # Change to true to enable
  start_time: "09:00"  # Start time
  end_time: "18:00"    # End time
  timezone: "Asia/Shanghai"  # Timezone
  push_once: true  # Push once per day
```

GitHub Actions will only generate news during specified hours.

### Q8: How to set password protection?

GitHub Pages doesn't support password protection natively, but you can:

**Method 1: Use private repository**
- Set repository to Private (requires GitHub Pro)
- Only you and authorized collaborators can access

**Method 2: Use third-party services**
- Use Cloudflare Access for authentication
- Deploy with Vercel or Netlify, which support password protection

**Method 3: Add simple verification in code**

Add after `<head>` tag in `index.html`:

```html
<script>
    // Simple password protection (insecure, demo only)
    const password = prompt('Enter access password:');
    if (password !== 'yourpassword') {
        document.body.innerHTML = '<h1>Incorrect Password</h1>';
        throw new Error('Incorrect password');
    }
</script>
```

**Note**: This method is insecure, only prevents casual visitors, can be easily bypassed.

### Q9: GitHub Actions failing, how to debug?

**Check steps:**

1. **View run logs**
   - Go to repository's **Actions** tab
   - Click failed workflow run
   - Expand each step to view error messages

2. **Common errors and solutions**

   | Error Message | Cause | Solution |
   |--------------|-------|----------|
   | `config/config.yaml does not exist` | Missing config file | Create config file per README |
   | `frequency_words.txt does not exist` | Missing keywords file | Create `config/frequency_words.txt` |
   | `Permission denied` | Insufficient permissions | Check Settings > Actions > Workflow permissions is "Read and write" |
   | API request failed | Network issue or rate limit | Wait and retry |

3. **Enable debug logging**
   
   Add in repository Settings > Secrets:
   - Name: `ACTIONS_RUNNER_DEBUG`
   - Value: `true`

### Q10: How to backup news data?

**Method 1: Auto backup to another repository**

Create `.github/workflows/backup.yml`:

```yaml
name: Backup News Data

on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly backup Sunday midnight
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

**Method 2: Manual download**

1. Visit your repository
2. Click green **Code** button
3. Select **Download ZIP**
4. Find all historical data in `output` directory after extraction

---

## 📞 Get Help

If you encounter issues:

1. **Check main documentation**: [README.md](../README.md)
2. **Search Issues**: [GitHub Issues](https://github.com/sansan0/TrendRadar/issues)
3. **Submit new Issue**: Describe your problem, environment, error logs in detail
4. **Community support**: Check contact information in main README

---

## 🎉 Deployment Success!

Congratulations on completing TrendRadar's GitHub Pages deployment! You can now:

- ✅ Check trending news anytime via web
- ✅ Share links with friends and colleagues
- ✅ Save as images to share on social media
- ✅ Access on mobile devices anytime

**Tip**: Remember to give the original project a ⭐ Star to support the author!
