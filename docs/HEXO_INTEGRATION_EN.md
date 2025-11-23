# Embedding TrendRadar into Hexo Blog

This document provides detailed instructions on how to integrate the TrendRadar news aggregation page into your Hexo personal blog.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Solution Overview](#solution-overview)
- [Solution 1: iframe Embedding (Recommended)](#solution-1-iframe-embedding-recommended)
- [Solution 2: Deploy to Subdirectory](#solution-2-deploy-to-subdirectory)
- [Solution 3: Custom Hexo Page](#solution-3-custom-hexo-page)
- [FAQ](#faq)

## Prerequisites

1. You have a running Hexo blog (e.g., `conf-haolee.github.io`)
2. You have forked the TrendRadar project and configured GitHub Actions
3. TrendRadar's GitHub Pages is enabled and working properly

## Solution Overview

Based on your needs and technical skills, here are three integration solutions:

| Solution | Difficulty | Pros | Cons |
|----------|------------|------|------|
| iframe Embedding | ⭐ Easy | Simple setup, easy maintenance | Cross-origin and responsive issues |
| Subdirectory Deploy | ⭐⭐ Medium | Full integration, SEO-friendly | Requires build configuration |
| Custom Page | ⭐⭐⭐ Complex | Full control over styling | Requires custom development |

## Solution 1: iframe Embedding (Recommended)

This is the simplest integration method, suitable for most users.

### Step 1: Create Hexo Page

In your Hexo blog root directory, run:

```bash
hexo new page trendradar
```

### Step 2: Edit Page Content

Edit the `source/trendradar/index.md` file:

```markdown
---
title: Trending News Aggregation
date: 2025-11-23
comments: false
---

<div style="width: 100%; height: 100vh; min-height: 800px;">
  <iframe 
    src="https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/" 
    style="width: 100%; height: 100%; border: none;"
    title="TrendRadar News Aggregation"
    loading="lazy">
  </iframe>
</div>

<style>
  /* Remove default Hexo content margins for full-screen iframe display */
  .post-body {
    padding: 0 !important;
  }
  .content {
    padding: 0 !important;
  }
</style>
```

**Note**: Replace `YOUR_GITHUB_USERNAME` with your GitHub username (e.g., `conf-haolee`)

### Step 3: Add to Navigation Menu

Edit your Hexo theme configuration file (usually `_config.yml` or `themes/your-theme/_config.yml`), add menu item:

```yaml
menu:
  Home: /
  Archives: /archives
  Trending News: /trendradar  # Add this line
  About: /about
```

### Step 4: Deploy

```bash
hexo clean
hexo generate
hexo deploy
```

### Pros
- ✅ Simple configuration, complete in 5 minutes
- ✅ TrendRadar and Hexo blog maintained independently
- ✅ TrendRadar updates automatically sync

### Cons
- ❌ iframe may not display well on some mobile devices
- ❌ Limited SEO optimization

## Solution 2: Deploy to Subdirectory

Deploy TrendRadar as a subdirectory of your Hexo blog.

### Step 1: Modify TrendRadar Repository

In your forked TrendRadar repository, modify `.github/workflows/crawler.yml`:

```yaml
      - name: Commit and push if changes
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          git add -A
          git diff --quiet && git diff --staged --quiet || (git commit -m "Auto update by GitHub Actions at $(TZ=Asia/Shanghai date)" && git push)
          
      # New: Push generated pages to Hexo blog repository
      - name: Deploy to Hexo blog
        env:
          HEXO_REPO_TOKEN: ${{ secrets.HEXO_REPO_TOKEN }}
        run: |
          # Clone your Hexo blog repository (replace with your repository URL)
          git clone https://${HEXO_REPO_TOKEN}@github.com/YOUR_USERNAME/YOUR_BLOG_REPO.git hexo-blog
          
          # Create trendradar directory
          mkdir -p hexo-blog/source/trendradar
          
          # Copy TrendRadar generated files
          cp index.html hexo-blog/source/trendradar/
          cp -r output hexo-blog/source/trendradar/
          
          # Commit and push
          cd hexo-blog
          git config user.name 'TrendRadar Bot'
          git config user.email 'bot@trendradar.com'
          git add source/trendradar
          git commit -m "Update TrendRadar: $(TZ=Asia/Shanghai date)" || exit 0
          git push
```

### Step 2: Configure GitHub Token

1. Create a Personal Access Token on GitHub (requires `repo` permission)
2. Add `HEXO_REPO_TOKEN` in TrendRadar repository's Settings → Secrets → Actions

### Step 3: Configure Hexo Skip Rendering

Edit `_config.yml` in Hexo root directory:

```yaml
skip_render:
  - trendradar/**
```

This prevents Hexo from processing TrendRadar's HTML files.

### Step 4: Add Navigation Link

Add menu item in theme configuration, pointing to `/trendradar/`.

### Pros
- ✅ Fully integrated into blog
- ✅ Unified domain and path
- ✅ SEO-friendly

### Cons
- ❌ Relatively complex configuration
- ❌ Requires GitHub Token configuration
- ❌ TrendRadar updates require Hexo rebuild

## Solution 3: Custom Hexo Page

Create a custom Hexo page template that directly embeds TrendRadar's HTML code.

### Step 1: Create Custom Layout

Create `layout/trendradar.ejs` in your Hexo theme directory:

```ejs
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trending News Analysis - <%= config.title %></title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <%- partial('_partial/head') %>
    <style>
        /* Paste TrendRadar CSS here */
    </style>
</head>
<body>
    <%- partial('_partial/header') %>
    
    <!-- Paste TrendRadar HTML content here -->
    <div class="container">
        <!-- TrendRadar content -->
    </div>
    
    <%- partial('_partial/footer') %>
    
    <script>
        /* Paste TrendRadar JavaScript here */
    </script>
</body>
</html>
```

### Step 2: Create Data Fetching Script

Create Hexo plugin or script to periodically fetch data from TrendRadar API and generate pages.

### Step 3: Configure Page

Create `source/trendradar/index.md`:

```markdown
---
layout: trendradar
title: Trending News Aggregation
---
```

### Pros
- ✅ Full control over styling and behavior
- ✅ Deep integration with Hexo theme

### Cons
- ❌ Complex implementation, requires strong frontend skills
- ❌ Manual synchronization of TrendRadar updates
- ❌ High maintenance cost

## Recommended Solutions Comparison

Based on different use cases:

### Quick Integration (Recommended for Beginners)
**Choose Solution 1**: iframe Embedding
- Suitable for: Quick TrendRadar display on blog
- Advantage: 5-minute setup, zero maintenance cost

### Professional Integration (Recommended for Advanced Users)
**Choose Solution 2**: Subdirectory Deployment
- Suitable for: Making TrendRadar part of your blog
- Advantage: SEO-friendly, good user experience

### Deep Customization (Recommended for Developers)
**Choose Solution 3**: Custom Page
- Suitable for: Need deep customization of styles and features
- Advantage: Full control, can add custom features

## FAQ

### Q1: iframe shows incomplete page?

**A**: Adjust iframe height:

```html
<iframe 
  src="https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/" 
  style="width: 100%; height: 120vh; border: none;"
  title="TrendRadar News Aggregation">
</iframe>
```

### Q2: How to make iframe height auto-adjust?

**A**: Add the following JavaScript code:

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

Also add to TrendRadar's `index.html`:

```javascript
// After page loads
window.parent.postMessage({
  height: document.body.scrollHeight
}, '*');
```

### Q3: TrendRadar updates not syncing to blog?

**A**: 
- For Solution 1 (iframe), clear browser cache
- For Solution 2 (subdirectory), re-run TrendRadar GitHub Actions

### Q4: Can I customize TrendRadar styles?

**A**: 
- Solution 1: Can override iframe styles via CSS (limited)
- Solution 2: Can directly modify TrendRadar style files
- Solution 3: Full customization

### Q5: How to display latest trending news on blog homepage?

**A**: Use Hexo plugin or custom component to fetch TrendRadar data via AJAX:

```javascript
fetch('https://YOUR_GITHUB_USERNAME.github.io/TrendRadar/output/latest-date/txt/report.txt')
  .then(response => response.text())
  .then(data => {
    // Parse and display data
    document.getElementById('trending-news').innerHTML = parseNews(data);
  });
```

## Advanced Tips

### 1. Add Refresh Button

Add a button to refresh TrendRadar on Hexo page:

```html
<button onclick="document.querySelector('iframe').contentWindow.location.reload()">
  Refresh News
</button>
```

### 2. Responsive Optimization

Optimize for mobile display:

```css
@media (max-width: 768px) {
  iframe {
    height: 100vh !important;
  }
}
```

### 3. Add Loading Animation

```html
<div id="loading" style="text-align: center; padding: 50px;">
  <p>Loading...</p>
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

## Summary

Choose the solution that fits your needs:

1. **Quick and Simple** → Solution 1 (iframe Embedding)
2. **Professional Integration** → Solution 2 (Subdirectory Deployment)
3. **Deep Customization** → Solution 3 (Custom Page)

Most users should start with **Solution 1** for the quickest integration, then upgrade to Solution 2 if needed.

## Related Links

- [TrendRadar Main Project](https://github.com/sansan0/TrendRadar)
- [Hexo Official Documentation](https://hexo.io/docs/)
- [GitHub Pages Documentation](https://docs.github.com/pages)

## Need Help?

If you encounter issues during integration:

1. Check [TrendRadar FAQ](https://github.com/sansan0/TrendRadar#问题答疑与交流)
2. Submit an Issue to [TrendRadar Repository](https://github.com/sansan0/TrendRadar/issues)
3. Refer to Hexo community discussions
