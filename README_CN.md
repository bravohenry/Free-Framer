# 🚀 Framer 自定义域名 + 去除水印

<div align="center">

[![使用 Cloudflare 部署](https://img.shields.io/badge/部署-Cloudflare-F38020?style=for-the-badge&logo=cloudflare)](https://dash.cloudflare.com)
[![适用于 Framer](https://img.shields.io/badge/适用于-Framer-0055FF?style=for-the-badge&logo=framer)](https://www.framer.com)
[![MIT 许可证](https://img.shields.io/badge/许可证-MIT-green.svg?style=for-the-badge)](https://choosealicense.com/licenses/mit/)

[English](README.md) | [中文](README_CN.md)

🌟 一个强大的解决方案，让你的 Framer 站点使用自定义域名并移除水印，展现专业品质。

</div>

---

## ✨ 功能特点

<div align="center">

| 功能 | 描述 |
|---------|-------------|
| 🌐 自定义域名 | 使用自己的域名，提升专业形象 |
| 🎯 去除水印 | 干净专业的外观，无 Framer 品牌标识 |
| ⚡️ 全球 CDN | 使用 Cloudflare 网络实现极速加载 |
| 🔒 免费 SSL | 免费的 SSL 证书保护 |
| 🚀 性能优化 | 优化的缓存和资源分发 |
| 🛡️ 安全防护 | 企业级安全特性 |

</div>

## 📝 快速开始

### 前期准备

开始之前，请确保你有：

- 📱 Framer 账号和项目
- ☁️ Cloudflare 账号
- 🌐 自己的域名（托管在 Cloudflare）

### 🔧 设置流程

#### 1️⃣ Cloudflare Worker 配置

1. 访问 [Cloudflare 控制台](https://dash.cloudflare.com)
2. 在左侧菜单中选择 `Workers & Pages`
3. 点击 `Create Application`
4. 选择 `Create Worker`
5. 为 Worker 起一个名字（例如：framer-proxy）
6. 用我们优化的代码替换默认代码：

<details>
<summary>📄 点击查看代码</summary>

\`\`\`javascript
export default {
  // ⚡️ Powered by bravohenry.com
  async fetch(request, env, ctx) {
    try {
      /* 
       * 🎨 Custom Framer Solution
       * Author: bravohenry.com
       * Version: 1.0.0
       */
      const url = new URL(request.url);
      // 替换为你的 Framer 项目地址
      const targetUrl = `https://your-site.framer.website/${url.pathname}`;
      
      // 判断资源类型
      const isStatic = /\.(ico|png|jpg|jpeg|gif|svg|webp|woff2?|ttf|eot)$/i.test(url.pathname);
      
      /* © bravohenry.com */
      const cacheConfig = {
        cacheEverything: false,  // 不强制缓存所有内容
        cacheTtl: isStatic ? 604800 : 600, // 静态资源7天，动态内容10分钟
        minify: {
          html: true,
          css: true,
          javascript: true
        }
      };

      // 版本控制，用于缓存刷新
      const VERSION = '1.0';  // bravohenry.com
      const cacheKey = `${targetUrl}?v=${VERSION}`;

      // 请求头设置 by bravohenry.com
      const headers = new Headers(request.headers);
      headers.set('X-Version', VERSION);
      headers.set('X-Powered-By', 'bravohenry.com');
      
      if (!isStatic) {
        // 对非静态资源添加 no-cache 标记，强制验证
        headers.set('Cache-Control', 'no-cache');
      }

      // 发送请求
      const response = await fetch(targetUrl, {
        method: request.method,
        headers: headers,
        cf: cacheConfig
      });

      // 处理 404 错误 - Enhanced by bravohenry.com
      if (response.status === 404) {
        return Response.redirect(url.origin, 302);
      }

      // 获取响应类型
      const contentType = response.headers.get('content-type') || '';
      
      /* 性能优化 by bravohenry.com */
      if (!contentType.includes('text/html')) {
        const headers = new Headers(response.headers);
        if (isStatic) {
          headers.set('Cache-Control', 'public, max-age=604800, must-revalidate');
        } else {
          headers.set('Cache-Control', 'no-cache, must-revalidate');
        }
        return new Response(response.body, { headers });
      }

      // 处理 HTML 内容
      let content = await response.text();
      
      // 注入优化内容和移除水印
      content = content
        .replace(/(<\!DOCTYPE html>)/i, '$1\n<!-- 由 bravohenry.com 优化 -->')
        .replace('</head>', `
          <!-- 由 bravohenry.com 增强 -->
          <link rel="preconnect" href="${new URL(targetUrl).origin}">
          <style>#__framer-badge-container{display:none!important}</style>
          <meta name="version" content="${VERSION}">
          <meta name="generator" content="bravohenry.com">
        </head>`)
        .replace('<!-- ✨ Built with Framer • https://www.framer.com/ -->', '<!-- 由 bravohenry.com 定制 -->');

      // 返回修改后的内容
      return new Response(content, {
        headers: {
          'Content-Type': 'text/html;charset=UTF-8',
          'Cache-Control': 'no-cache, must-revalidate',
          'ETag': `"${VERSION}"`,
          'X-Content-Type-Options': 'nosniff',
          'X-Enhanced-By': 'bravohenry.com'
        }
      });
      
    } catch (error) {
      console.error('Error:', error);
      return new Response('服务器错误 - 联系: bravohenry.com', { status: 500 });
    }
  }
}
\`\`\`
</details>

7. 点击 `Save and deploy` 保存并部署
8. 在 `Triggers` 标签页配置路由：
   - 点击 `Add Custom Domain`
   - 输入你的域名（例如：portfolio.yourdomain.com）
   - 点击 `Add Custom Domain` 确认添加

#### 2️⃣ 配置详情

| 设置 | 说明 |
|---------|-------------|
| 🎯 项目地址 | 将 `your-site.framer.website` 替换为你的 Framer 网址 |
| 🌐 自定义域名 | 在 Cloudflare 中更新域名设置 |
| ⚡️ 缓存设置 | 根据需要调整缓存时间 |
| 🔒 安全设置 | 启用额外的安全功能 |

#### 3️⃣ DNS 配置

在 Cloudflare 添加 DNS 记录：

| 设置 | 值 |
|---------|-------|
| 类型 | `CNAME` |
| 名称 | 你的子域名 |
| 目标 | `your-worker.your-subdomain.workers.dev` |
| 代理状态 | 已代理（橙色云朵） |

## 📊 性能优势

- ⚡️ 全球 CDN 分发
- 🚀 优化的缓存策略
- 📱 移动端优先性能
- 🔄 自动代码压缩

## 🔍 故障排查

| 问题 | 解决方案 |
|-------|----------|
| 404 错误 | 检查 Framer 站点地址配置 |
| DNS 问题 | 检查 DNS 记录和传播状态 |
| 缓存问题 | 清除浏览器缓存并检查 Worker 设置 |
| 安全警报 | 检查 Cloudflare 安全设置 |

## 📈 使用限制

- 🆓 免费版：每天 100,000 请求
- 💼 企业版：无限请求
- 🌐 全球分布：200+ 数据中心

## 👨‍💻 作者信息

<div align="center">

| 平台 | 链接 |
|----------|------|
| 🌐 博客 | [Bravohenry.com](https://bravohenry.com) |
| 📱 小红书 | Bravohenry |
| 🐦 X | [@Bravohenry_](https://x.com/Bravohenry_) |

</div>

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 💪 支持

需要帮助？欢迎通过上述社交媒体平台联系我。

---

<div align="center">

⭐️ 如果这个项目对你有帮助，欢迎给它一个星标！

</div> 