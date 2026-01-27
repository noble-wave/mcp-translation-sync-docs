# MCP Translation Sync Documentation

Welcome to the MCP Translation Sync documentation! 👋

## 🚀 New Here?

Start with the **[Home Page](home.md)** for an overview and quick start guide.

## 📚 Documentation

### Getting Started
- **[🏠 Home](home.md)** - Product overview, features, and quick start
- **[⚡ Quick Reference](QUICKREF.md)** - Commands and settings at a glance
- **[📖 User Guide](user-guide.md)** - Complete installation and usage guide

### Configuration & Advanced
- **[⚙️ Configuration](configuration.md)** - All settings and options
- **[🔀 Git Strategies](git-strategies.md)** - Advanced change detection
- **[❓ FAQ](faq.md)** - Common questions and troubleshooting
- **[📝 Changelog](changelog.md)** - Version history and updates

## 🎯 Quick Links

- [Install Extension](https://marketplace.visualstudio.com/items?itemName=noble-wave.mcp-translation-sync)
- [Report Issues](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new)
- [Email Support](mailto:mcp@noble-wave.com)

## 📋 Requirements

- **VS Code** 1.80.0 or later
- **GitHub Copilot** extension (for AI translations)
- **Node.js** 18+ (for development)
- **Git** (optional, for change detection)

## 🔄 How It Works

```
1. First time: Discover translation files → Auto-detected and saved
2. Edit source language file (e.g., en.json) → Add/change/remove keys
3. Run Sync → GitHub Copilot translates intelligently
4. All Done → All language files updated & sorted
```

### Smart Translation Logic

**Reuses translations when**:
- Translation exists and is valid
- Source language hasn't changed (git-detected)
- Placeholders match correctly

**Translates when**:
- Key is new
- Source language value changed
- Marked with `TODO:` prefix
- Placeholder mismatch detected

## ⚙️ Quick Configuration

*Auto-configured by `MCP: Discover Translation Files` command:*

```json
{
  "mcpTranslation.translation.projectRoot": ".",
  "mcpTranslation.translation.sourceLanguage": "en",
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/en.json", "language": "en", "enabled": true },
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true }
  ]
}
```

**See** [Configuration](configuration.md) for all options.

## 🔍 Supported Placeholders

Automatically validates and preserves:

- `{name}`, `{{count}}` - Angular, Vue
- `%s`, `%d` - C, PHP, Python
- `%(name)s` - Python named
- `${var}` - JavaScript template literals
- `[key]` - Bracket notation

## 🤝 Support

- **Report Issues**: [Open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new) on our public documentation repository
- **Email**: [mcp@noble-wave.com](mailto:mcp@noble-wave.com)

---

**Made with ❤️ by [Noble Wave](https://noble-wave.com/)**

> 💡 **For Contributors**: Technical and architecture documentation is in the `/dev` folder (internal use).
> This `/docs` folder contains user-facing documentation only.
