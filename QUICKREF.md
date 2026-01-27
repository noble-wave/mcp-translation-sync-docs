# MCP Translation Server - Quick Reference

**AI-Powered Translation Sync for i18n Projects**

---

## 🎯 What Is It?

Keep your translation files synchronized with one command. Edit your source language file once, let GitHub Copilot translate the rest.

**Perfect for**: Product teams, developers, and translators working with internationalized applications.

---

## ✨ Key Features (30-second pitch)

| Feature | Benefit |
|---------|---------|
| 🤖 **AI-Powered** | GitHub Copilot understands context, not just words |
| 🎯 **Smart Reuse** | Only translates what changed - saves time & API calls |
| 🔍 **Placeholder Safe** | Preserves `{variables}` automatically |
| 📝 **Git-Aware** | Detects source language changes via git history |
| ⚡ **Fast** | Parallel processing, handles 50+ languages |
| 🎨 **Clean** | Alphabetically sorted, consistent structure |
| 💬 **Natural** | Chat with `@translate sync` - it just works |

---

## 🚀 30-Second Setup

```bash
# 1. Install
VS Code Extensions → Search "MCP Translation Sync"

# 2. Discover Files (Ctrl+Shift+P)
"MCP: Discover Translation Files"  # Auto-detects all translation files

# 3. (Optional) Check Health
"MCP: Check Translation Health"  # View workspace health status

# 4. (Optional) Review Settings
"MCP: View Settings"  # View and edit configuration

# 5. Sync (Ctrl+Shift+P)
"MCP: Sync Translations"

# Done! All translations updated.
```

---

## 💡 Use Cases

### For Product Managers
**Problem**: Manual translation updates across 20+ files takes hours  
**Solution**: One command, all files updated automatically  
**Result**: Ship features faster, stay in sync

### For Developers
**Problem**: Forgetting to update translations breaks builds  
**Solution**: Git-aware detection knows what changed  
**Result**: Catch missing translations before commit

### For Translators
**Problem**: Missing placeholders break app functionality  
**Solution**: Automatic validation catches errors  
**Result**: Quality translations, fewer bugs

---

## 🎓 How It Works (60-second explanation)

```
1. You edit source language file → Add/change/remove keys

2. Run Sync → Extension does:
   ✓ Reads source language file as source of truth
   ✓ Checks git history for changes
   ✓ Compares with all language files
   ✓ Reuses valid existing translations
   ✓ Translates only what's needed
   ✓ Validates placeholders
   ✓ Sorts keys alphabetically
   ✓ Writes updated files

3. All Done → Commit everything together

4. Team sees clean, consistent translations
```

---

## 📊 Real-World Impact

### Before MCP Translation Server
- ⏱️ 2-3 hours per feature for translation updates
- 🐛 Frequent placeholder mismatches in production
- 📝 Manual tracking of what needs translation
- 💸 Redundant API calls for unchanged text
- 🔄 Inconsistent key ordering across files

### After MCP Translation Server
- ⚡ 30 seconds per feature (fully automated)
- ✅ Zero placeholder errors (automatic validation)
- 🤖 Git knows exactly what changed
- 💰 70% reduction in API calls (smart reuse)
- 🎨 Perfect consistency (alphabetical sorting)

---

## 🎯 Target Audience

### Primary Users
- **Frontend Developers** working with i18n libraries (React, Angular, Vue)
- **Product Teams** shipping features in multiple languages
- **DevOps Engineers** automating translation workflows

### Perfect For
- ✅ SaaS applications with 5+ languages
- ✅ Mobile apps with frequent updates
- ✅ Web apps with complex localization
- ✅ Teams using GitHub Copilot
- ✅ Projects with 10+ translation files

### Not Ideal For
- ❌ Single-language projects
- ❌ Teams without GitHub Copilot
- ❌ Projects with non-JSON translation formats (yet)

---

## 🎬 Demo Script (5 minutes)

### Setup (1 minute)
```bash
# Show project with en.json and fr.json
# Show en.json has 10 keys
# Show fr.json has 10 translations
```

### Add New Feature (2 minutes)
```bash
# Add 3 new keys to source language file (en.json)
# Show they include placeholders: {name}, {count}
# Run "MCP: Sync Translations"
# Show progress notification
# Show output panel logs
```

### Review Results (2 minutes)
```bash
# Open fr.json - 3 new keys added
# Placeholders preserved correctly
# Keys alphabetically sorted
# Existing 10 translations unchanged (reused)
# Show summary: "+3 new, =10 reused"
```

---

## 📞 Sales Talking Points

### For Decision Makers
1. **Productivity**: 95% time savings on translation updates
2. **Quality**: Zero placeholder errors with automatic validation
3. **Cost**: Free extension (requires existing Copilot license)
4. **ROI**: Instant - saves hours from day one
5. **Risk**: Low - preview mode lets you review before applying

### For Technical Leads
1. **Integration**: Native VS Code, zero configuration
2. **Flexibility**: Works with any i18n library
3. **Git Integration**: 6 strategies for any workflow
4. **Customization**: 40+ settings for fine-tuning
5. **Architecture**: Clean, extensible, well-documented

### For Developers
1. **Fast**: One command, done in seconds
2. **Smart**: Reuses translations, saves API calls
3. **Safe**: Validates placeholders, catches errors
4. **Natural**: Chat integration, no memorizing commands
5. **Open Source**: MIT license, contribute improvements

---

## 🚀 Call to Action

### Try It Free
```
1. Install from VS Code Marketplace
2. Configure your paths
3. Run first sync
4. See results in 30 seconds
```

**No credit card. No trial. Just install and use.**

### Get Started
- **Documentation**: https://mcp-translation-sync.noble-wave.com/#/user-guide
- **Report Issues**: [Open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new)
- **Email Support**: [mcp@noble-wave.com](mailto:mcp@noble-wave.com)

---

## 📄 One-Page Summary

**What**: AI-powered translation sync for i18n projects  
**How**: Edit source language file → Run sync → All translations updated  
**Why**: Saves hours, prevents errors, stays consistent  
**Who**: Teams using GitHub Copilot with 5+ languages  
**Price**: Free (requires Copilot subscription)  
**Setup**: 30 seconds  
**ROI**: Instant (saves hours from first use)

**Install Now**: VS Code Extensions → "MCP Translation Sync"

---

**Questions?** Visit our [documentation](https://mcp-translation-sync.noble-wave.com) or [open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new)
