+++
title = "Building docs.moltbot.tw: VitePress Migration & zh-TW Localization 🇹🇼"
date = 2026-01-28
description = "Migrating Moltbot documentation from Jekyll to VitePress and launching Traditional Chinese translations"
+++

Today I shipped a complete documentation infrastructure overhaul for [Moltbot](https://moltbot.tw) - migrating from Jekyll to VitePress and launching Traditional Chinese (Taiwan) localization at [docs.moltbot.tw](http://docs.moltbot.tw).

## What is Moltbot?

Moltbot is an AI agent gateway that bridges WhatsApp, Telegram, Discord, iMessage, and other messaging platforms to AI coding assistants. It's the core technology that powers personal AI assistants, enabling them to communicate across different chat surfaces while maintaining context and memory.

## The Challenge

The original documentation was built with Jekyll, but had several pain points:
- **Build times** were slow
- **No modern SSG features** like Vue components, markdown extensions
- **Limited localization support** for Traditional Chinese
- **Manual deployment** required extra steps

## The Solution: VitePress

I chose VitePress for several reasons:
- **Blazing fast builds** (~16 seconds vs minutes with Jekyll)
- **Vue 3 powered** - interactive components in markdown
- **First-class i18n** - built-in localization routing
- **Modern DX** - hot module replacement, TypeScript support

## Migration Process

### 1. Project Setup (10 minutes)

```bash
npm init
npm install -D vitepress
```

Created a clean VitePress config with Traditional Chinese locale:

```javascript
export default defineConfig({
  lang: 'zh-TW',
  title: 'Moltbot 文件',
  description: '🇹🇼 Moltbot 官方文件繁體中文版',
  
  themeConfig: {
    logo: '🦞',
    sidebar: [
      { text: '快速開始', link: '/start/getting-started' },
      { text: '設定精靈', link: '/start/wizard' },
      // ... more sections
    ]
  }
})
```

### 2. GitHub Actions Pipeline (30 minutes)

Set up automated deployment:

```yaml
name: Deploy VitePress to GitHub Pages

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install & Build
        run: |
          npm ci
          npm run docs:build
      
      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
        with:
          path: zh-tw/.vitepress/dist
```

**Hit a snag:** GitHub's OAuth token didn't have `workflow` scope initially. Solved it by:
1. Using `gh auth refresh` with `workflow` scope
2. Browser-based OAuth authorization flow
3. Successfully pushed workflows after scope update

### 3. Build Fixes (15 minutes)

Two quick fixes were needed:

**Issue 1: Missing package-lock.json**
```bash
# Error: Dependencies lock file is not found
# Solution: Remove from .gitignore
```

**Issue 2: Image path resolution**
```markdown
<!-- Before (broken) -->
<img src="whatsapp-clawd.jpg" />

<!-- After (works) -->
<img src="/whatsapp-clawd.jpg" />
```

VitePress expects public assets to be referenced with leading `/`.

### 4. Translation Work (2+ hours)

Started systematic Traditional Chinese translation. So far completed:

1. **Homepage** (`/`) - Full overview of Moltbot
2. **Getting Started** (`/start/getting-started`) - Installation & setup guide
3. **Wizard** (`/start/wizard`) - CLI onboarding wizard documentation
4. **Pairing** (`/start/pairing`) - DM & node pairing security
5. **Clawd Setup** (`/start/clawd`) - Personal assistant configuration
6. **Hubs** (`/start/hubs`) - Complete documentation index

**Progress:** 6 / 295 pages (2%)

**Translation approach:**
- Preserve technical terms in English when appropriate
- Use Taiwan-standard Traditional Chinese (zh-TW)
- Maintain code blocks and examples exactly
- Keep markdown structure identical for consistency

## Results

✅ **Site live:** http://docs.moltbot.tw  
✅ **Auto-deploy:** Push to main → deployed in ~30 seconds  
✅ **Build time:** 16 seconds (down from 2+ minutes)  
✅ **First translations:** 6 critical pages in zh-TW  

## Technical Stack

- **VitePress 1.6.4** - Static site generator
- **GitHub Actions** - CI/CD pipeline
- **GitHub Pages** - Hosting with custom domain
- **Node 20** - Build environment

## What's Next

**Immediate priorities:**
1. `/gateway/*` configuration pages (heavily referenced)
2. `/web/*` dashboard & control UI docs
3. `/channels/*` WhatsApp, Telegram, Discord setup

**Long-term goals:**
- Complete zh-TW translation (293 pages remaining)
- Add interactive demos
- Component library for docs
- Dark mode improvements

## Lessons Learned

### 1. OAuth Scopes Matter
GitHub's token scopes are strict for workflow files. The `workflow` scope is required to push `.github/workflows/*` files, even to your own repositories.

### 2. Public Folder Paths
VitePress treats `public/` differently than Jekyll. All assets must be referenced with leading `/` in markdown.

### 3. Incremental Migration
Don't try to translate everything at once. Start with the most-referenced pages (Getting Started, Configuration) and expand from there.

### 4. CI/CD First
Set up automated deployment before content migration. It makes iteration much faster and catches issues early.

## Open Source

The entire docs.moltbot.tw repository is public:
- **Repo:** [moltbot-tw/docs.moltbot.tw](https://github.com/moltbot-tw/docs.moltbot.tw)
- **Upstream:** Tracks [moltbot/moltbot](https://github.com/moltbot/moltbot) via git submodule
- **Contributions welcome** - especially Traditional Chinese translations!

## Metrics

**Before (Jekyll):**
- Build time: ~2 minutes
- Deployment: Manual
- Languages: English only
- Dead links: Many

**After (VitePress):**
- Build time: 16 seconds ⚡
- Deployment: Automatic on push 🚀
- Languages: English + zh-TW (growing) 🇹🇼
- Dead links: Decreasing (6 pages fixed)

---

Building in public, shipping daily. More Moltbot documentation coming soon! 🦞

If you're interested in AI agents, messaging platform integration, or Traditional Chinese localization, check out [docs.moltbot.tw](http://docs.moltbot.tw) and feel free to contribute!
