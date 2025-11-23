---
title: 热点新闻聚合
date: 2025-11-23
comments: false
---

<!-- TrendRadar 嵌入示例 -->

<div id="trendradar-wrapper" style="width: 100%; position: relative;">
  <!-- 加载提示 -->
  <div id="loading-indicator" style="text-align: center; padding: 100px 20px;">
    <div style="display: inline-block;">
      <div style="width: 50px; height: 50px; border: 4px solid #f3f3f3; border-top: 4px solid #4f46e5; border-radius: 50%; animation: spin 1s linear infinite;"></div>
      <p style="margin-top: 20px; color: #666; font-size: 14px;">加载中...</p>
    </div>
  </div>
  
  <!-- TrendRadar iframe -->
  <iframe 
    id="trendradar-iframe"
    src="https://你的用户名.github.io/TrendRadar/" 
    style="display: none; width: 100%; height: 100vh; min-height: 800px; border: none; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);"
    title="TrendRadar 新闻聚合"
    loading="lazy"
    onload="handleIframeLoad()">
  </iframe>
  
  <!-- 错误提示 -->
  <div id="error-message" style="display: none; text-align: center; padding: 100px 20px;">
    <div style="max-width: 500px; margin: 0 auto;">
      <h3 style="color: #dc2626; margin-bottom: 16px;">⚠️ 加载失败</h3>
      <p style="color: #666; margin-bottom: 24px;">无法加载 TrendRadar 页面，请检查配置或稍后重试。</p>
      <button onclick="retryLoad()" style="background: #4f46e5; color: white; border: none; padding: 10px 24px; border-radius: 6px; cursor: pointer; font-size: 14px;">
        重试
      </button>
    </div>
  </div>
</div>

<style>
  /* 移除 Hexo 默认的内容边距 */
  .post-body {
    padding: 0 !important;
  }
  
  .content {
    padding: 0 !important;
    max-width: 100% !important;
  }
  
  .post {
    padding: 0 !important;
  }
  
  /* 加载动画 */
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
  
  /* 响应式调整 */
  @media (max-width: 768px) {
    #trendradar-iframe {
      height: 150vh !important;
      min-height: 600px !important;
    }
  }
</style>

<script>
  // iframe 加载成功处理
  function handleIframeLoad() {
    setTimeout(function() {
      document.getElementById('loading-indicator').style.display = 'none';
      document.getElementById('trendradar-iframe').style.display = 'block';
    }, 500);
  }
  
  // 重试加载
  function retryLoad() {
    document.getElementById('error-message').style.display = 'none';
    document.getElementById('loading-indicator').style.display = 'block';
    var iframe = document.getElementById('trendradar-iframe');
    iframe.src = iframe.src;
  }
  
  // iframe 加载超时处理
  setTimeout(function() {
    var loading = document.getElementById('loading-indicator');
    var iframe = document.getElementById('trendradar-iframe');
    
    if (loading.style.display !== 'none') {
      loading.style.display = 'none';
      document.getElementById('error-message').style.display = 'block';
    }
  }, 15000); // 15秒超时
  
  // 监听 iframe 内容高度变化（可选，需要 TrendRadar 配合）
  window.addEventListener('message', function(e) {
    // 确保消息来自可信源
    if (e.origin.includes('github.io')) {
      var iframe = document.getElementById('trendradar-iframe');
      if (e.data.height) {
        iframe.style.height = e.data.height + 'px';
      }
    }
  });
</script>

<!-- 
使用说明：
1. 将此文件保存为 source/trendradar/index.md
2. 将 "你的用户名" 替换为您的 GitHub 用户名（例如：conf-haolee）
3. 运行 hexo clean && hexo generate && hexo deploy
4. 在主题配置中添加导航菜单项

配置导航菜单示例：
menu:
  首页: /
  归档: /archives
  热点新闻: /trendradar
  关于: /about

更多集成方案请参考：
https://github.com/sansan0/TrendRadar/blob/main/docs/HEXO_INTEGRATION.md
-->
