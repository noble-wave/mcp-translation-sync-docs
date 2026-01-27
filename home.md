<div class="hero-section">
  <h1>🌍 MCP Translation Sync</h1>
  <p>AI-Powered Translation Sync for i18n Projects</p>
  <p style="font-size: 1.1em; opacity: 0.9;">Edit your source language file once. Let GitHub Copilot translate the rest.</p>
  <div class="hero-buttons">
    <a href="#/user-guide" class="hero-button">Get Started →</a>
    <a href="https://marketplace.visualstudio.com/items?itemName=noble-wave.mcp-translation-sync" class="hero-button secondary" target="_blank">Install Extension</a>
  </div>
</div>

## Why MCP Translation Sync?

<div class="feature-grid">
  <div class="feature-card">
    <h3>🤖 AI-Powered</h3>
    <p>GitHub Copilot understands context and delivers natural translations, not just word-for-word conversions.</p>
  </div>
  <div class="feature-card">
    <h3>⚡ Lightning Fast</h3>
    <p>Batch processing with smart reuse, configurable batch size, and git-aware detection typically reduces API calls by up to 95%, making sync of 50+ languages very fast.</p>
  </div>
  <div class="feature-card">
    <h3>🎯 Smart Reuse</h3>
    <p>Only translates what changed. Existing valid translations stay untouched, saving time and money.</p>
  </div>
  <div class="feature-card">
    <h3>🔍 Placeholder Safe</h3>
    <p>Automatically validates and preserves variables like <code>{name}</code>, <code>%s</code>, <code>{{count}}</code>.</p>
  </div>
  <div class="feature-card">
    <h3>📝 Git-Aware</h3>
    <p>Six comparison strategies detect source changes via git history. Only retranslates what needs updating.</p>
  </div>
  <div class="feature-card">
    <h3>💬 Natural Commands</h3>
    <p>Chat with <code>@translate sync only French</code> - it just works. Runtime overrides without config changes.</p>
  </div>
</div>

## Real Impact

<div class="stats-grid">
  <div class="stat-card">
    <div class="stat-number">Up to 95%</div>
    <div>Fewer API Calls (batching + reuse)</div>
  </div>
  <div class="stat-card">
    <div class="stat-number">100+</div>
    <div>Languages Supported</div>
  </div>
  <div class="stat-card">
    <div class="stat-number">30s</div>
    <div>Setup Time</div>
  </div>
  <div class="stat-card">
    <div class="stat-number">0</div>
    <div>Manual Updates</div>
  </div>
</div>

## Perfect For

<span class="badge">Product Teams</span>
<span class="badge">Frontend Developers</span>
<span class="badge">DevOps Engineers</span>
<span class="badge">Translators</span>

### Your Workflow, Simplified

```bash
# Before: Manual updates across 20+ files, 2-3 hours per feature
# After: One command, 30 seconds, fully automated

1. Edit source language file (en.json)
2. Run: @translate sync
3. Done! All translations updated & sorted
```

## Quick Start

<!-- tabs:start -->

#### **Extension**

```bash
# 1. Install from VS Code Marketplace
Search "MCP Translation Sync" → Install

# 2. Discover files (auto-detect)
Ctrl+Shift+P → "MCP: Discover Translation Files"

# 3. Sync translations
Ctrl+Shift+P → "MCP: Sync Translations"

# Or use chat:
@translate sync
```

<video width="100%" autoplay loop muted playsinline>
  <source src="assets/translation_discover.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

#### **Chat Commands**

```bash
# Discover translation files
@translate /discover

# Check translation health
@translate /health

# Sync all languages
@translate sync

# Sync specific languages only
@translate sync only French and German

# Exclude languages
@translate sync except Spanish

# Override settings temporarily
@translate sync with temperature 0.8
@translate sync staged changes
@translate sync last 3 commits
```

<!-- tabs:end -->

## Key Features

### 🔄 Translation Sync
- **Source of truth**: One file defines structure, all others mirror it
- **Smart detection**: Finds new keys, removed keys, changed values
- **Intelligent reuse**: Keeps valid translations, retranslates only when needed
- **Alphabetical sorting**: Clean, consistent structure across all files

### 🎨 Developer Experience
- **Zero config**: Works instantly with sensible defaults
- **Auto-discovery**: Finds all translation files automatically
- **Progress tracking**: Real-time updates during sync
- **Dry-run mode**: Preview changes before applying
- **Cancellable**: Stop long operations anytime

### ⚙️ Advanced Capabilities
- **Runtime overrides**: Change settings from chat without modifying config
- **Git strategies**: 6 comparison modes (HEAD vs working, staged, last N commits, etc.)
- **Selective sync**: Translate specific languages on demand
- **Batch processing**: Configurable batching for optimal performance
- **Custom instructions**: Fine-tune translation tone and terminology

### 🌐 Language Support
All valid BCP-47 language tags via `Intl.Locale` validation:
- Simple: `en`, `fr`, `de`, `es`, `ja`, `zh`, `ko`, `ar`, `ru`, `pt`, `it`, `nl`, `pl`, `tr`, `sv`
- Regional: `en-US`, `en-GB`, `pt-BR`, `zh-CN`, `fr-CA`, `es-MX`
- Script variants: `zh-Hans`, `zh-Hant`, `sr-Cyrl`, `sr-Latn`

## Use Cases

### SaaS Applications
> "Saves us hours every sprint. Can't imagine going back to manual translation updates."  
> — *Frontend Team Lead, SaaS Company*

**Problem**: Manual translation updates across 20+ files takes hours  
**Solution**: One command, all files updated automatically  
**Result**: Ship features faster, stay in sync

### E-commerce Platforms
> "The git-aware detection is brilliant. Only translates what actually changed."  
> — *Engineering Manager, E-commerce*

**Problem**: Frequent updates require constant translation syncing  
**Solution**: Git-aware detection knows exactly what changed  
**Result**: 70% reduction in API calls, faster deployments

### Mobile Apps
> "Placeholder validation caught bugs we didn't even know we had."  
> — *QA Engineer, Mobile App*

**Problem**: Missing placeholders break app functionality  
**Solution**: Automatic validation catches errors  
**Result**: Zero placeholder bugs in production

## Documentation

<div class="feature-grid">
  <div class="feature-card">
    <h3>📖 <a href="#/user-guide">User Guide</a></h3>
    <p>Installation, first sync, daily workflow, troubleshooting</p>
  </div>
  <div class="feature-card">
    <h3>⚙️ <a href="#/configuration">Configuration</a></h3>
    <p>All settings, environment variables, runtime overrides</p>
  </div>
  <div class="feature-card">
    <h3>🔀 <a href="#/git-strategies">Git Strategies</a></h3>
    <p>Advanced change detection, comparison modes</p>
  </div>
  <div class="feature-card">
    <h3>❓ <a href="#/faq">FAQ</a></h3>
    <p>Common questions, troubleshooting, best practices</p>
  </div>
</div>

## Requirements

- **VS Code** 1.80.0 or later
- **GitHub Copilot** extension (for AI translations)
- **Node.js** 18+ (for development)
- **Git** (optional, for change detection)

## Open Source

MIT Licensed · Free Forever · Community Driven

<div style="text-align: center; margin: 40px 0;">
  <a href="https://marketplace.visualstudio.com/items?itemName=noble-wave.mcp-translation-sync" style="display: inline-block; padding: 15px 40px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; text-decoration: none; border-radius: 8px; font-weight: 600; font-size: 1.1em;">
    Install Now - It's Free →
  </a>
</div>

---

<div style="text-align: center; color: #666; margin-top: 60px;">
  Made with ❤️ by <a href="https://noble-wave.com/" target="_blank">Noble Wave</a>
</div>
