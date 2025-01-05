# 🚀 Framer Custom Domain + Watermark Removal

<div align="center">

[![Deploy with Cloudflare](https://img.shields.io/badge/Deploy-Cloudflare-F38020?style=for-the-badge&logo=cloudflare)](https://dash.cloudflare.com)
[![Made for Framer](https://img.shields.io/badge/Made_for-Framer-0055FF?style=for-the-badge&logo=framer)](https://www.framer.com)
[![MIT License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](https://choosealicense.com/licenses/mit/)

[English](README.md) | [中文](README_CN.md)

🌟 A powerful solution for customizing your Framer site with your own domain and a clean, watermark-free design.

</div>

---

## ✨ Features

<div align="center">

| Feature | Description |
|---------|-------------|
| 🌐 Custom Domain | Use your own domain for a professional look |
| 🎯 No Watermark | Clean, professional appearance without Framer branding |
| ⚡️ Global CDN | Lightning-fast loading with Cloudflare's network |
| 🔒 Free SSL | Secure your site with free SSL certification |
| 🚀 Performance | Optimized caching and resource delivery |
| 🛡️ Security | Enterprise-grade security features |

</div>

## 📝 Quick Start

### Prerequisites

Before you begin, ensure you have:

- 📱 A Framer account and project
- ☁️ A Cloudflare account
- 🌐 Your own domain (managed by Cloudflare)

### 🔧 Setup Process

#### 1️⃣ Cloudflare Worker Setup

1. Access your [Cloudflare Dashboard](https://dash.cloudflare.com)
2. Navigate to `Workers & Pages` in the sidebar
3. Click `Create Application`
4. Select `Create Worker`
5. Name your Worker (e.g., framer-proxy)
6. Replace the default code with our optimized solution:

<details>
<summary>📄 Click to view the code</summary>

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
      // Replace with your Framer project URL
      const targetUrl = \`https://your-site.framer.website/\${url.pathname}\`;
      
      // Detect static resources
      const isStatic = /\.(ico|png|jpg|jpeg|gif|svg|webp|woff2?|ttf|eot)$/i.test(url.pathname);
      
      /* © bravohenry.com */
      const cacheConfig = {
        cacheEverything: false,
        cacheTtl: isStatic ? 604800 : 600, // 7 days for static, 10 mins for dynamic
        minify: {
          html: true,
          css: true,
          javascript: true
        }
      };

      // Version control for cache
      const VERSION = '1.0'; // bravohenry.com
      const cacheKey = \`\${targetUrl}?v=\${VERSION}\`;

      // Request headers setup by bravohenry.com
      const headers = new Headers(request.headers);
      headers.set('X-Version', VERSION);
      headers.set('X-Powered-By', 'bravohenry.com');
      
      if (!isStatic) {
        headers.set('Cache-Control', 'no-cache');
      }

      // Forward request
      const response = await fetch(targetUrl, {
        method: request.method,
        headers: headers,
        cf: cacheConfig
      });

      // Handle 404 - Enhanced by bravohenry.com
      if (response.status === 404) {
        return Response.redirect(url.origin, 302);
      }

      // Get content type
      const contentType = response.headers.get('content-type') || '';
      
      /* Performance optimization by bravohenry.com */
      if (!contentType.includes('text/html')) {
        const headers = new Headers(response.headers);
        if (isStatic) {
          headers.set('Cache-Control', 'public, max-age=604800, must-revalidate');
        } else {
          headers.set('Cache-Control', 'no-cache, must-revalidate');
        }
        return new Response(response.body, { headers });
      }

      // Process HTML content
      let content = await response.text();
      
      // Inject optimizations and remove watermark
      content = content
        .replace(/(<\!DOCTYPE html>)/i, '$1\\n<!-- Optimized by bravohenry.com -->')
        .replace('</head>', \`
          <!-- Enhanced by bravohenry.com -->
          <link rel="preconnect" href="\${new URL(targetUrl).origin}">
          <style>#__framer-badge-container{display:none!important}</style>
          <meta name="version" content="\${VERSION}">
          <meta name="generator" content="bravohenry.com">
        </head>\`)
        .replace('<!-- ✨ Built with Framer • https://www.framer.com/ -->', '<!-- Customized by bravohenry.com -->');

      // Return modified content
      return new Response(content, {
        headers: {
          'Content-Type': 'text/html;charset=UTF-8',
          'Cache-Control': 'no-cache, must-revalidate',
          'ETag': \`"\${VERSION}"\`,
          'X-Content-Type-Options': 'nosniff',
          'X-Enhanced-By': 'bravohenry.com'
        }
      });
      
    } catch (error) {
      console.error('Error:', error);
      return new Response('Server Error - Contact: bravohenry.com', { status: 500 });
    }
  }
}
\`\`\`
</details>

7. Click `Save and deploy`
8. Configure in `Triggers` tab:
   - Click `Add Custom Domain`
   - Enter your domain (e.g., portfolio.yourdomain.com)
   - Confirm by clicking `Add Custom Domain`

#### 2️⃣ Configuration Details

| Setting | Description |
|---------|-------------|
| 🎯 Project URL | Replace `your-site.framer.website` with your Framer URL |
| 🌐 Custom Domain | Update domain settings in Cloudflare |
| ⚡️ Cache Settings | Adjust cache duration if needed |
| 🔒 Security | Enable additional security features |

#### 3️⃣ DNS Configuration

Add a DNS record in Cloudflare:

| Setting | Value |
|---------|-------|
| Type | `CNAME` |
| Name | Your subdomain |
| Target | `your-worker.your-subdomain.workers.dev` |
| Proxy | Proxied (orange cloud) |

## 📊 Performance

- ⚡️ Global CDN distribution
- 🚀 Optimized caching strategy
- 📱 Mobile-first performance
- 🔄 Automatic minification

## 🔍 Troubleshooting

| Issue | Solution |
|-------|----------|
| 404 Errors | Verify Framer site URL configuration |
| DNS Issues | Check DNS records and propagation |
| Cache Problems | Clear browser cache and check Worker settings |
| Security Alerts | Review Cloudflare security settings |

## 📈 Usage Limits

- 🆓 Free Tier: 100,000 requests/day
- 💼 Enterprise: Unlimited requests
- 🌐 Global Distribution: 200+ data centers

## 👨‍💻 Author

<div align="center">

| Platform | Link |
|----------|------|
| 🌐 Blog | [Bravohenry.com](https://bravohenry.com) |
| 📱 Xiaohongshu | Bravohenry |
| 🐦 X | [@Bravohenry_](https://x.com/Bravohenry_) |

</div>

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 💪 Support

Need help? Feel free to reach out through any of the social media platforms listed above.

---

<div align="center">

⭐️ If this project helps you, please consider giving it a star!

</div> 