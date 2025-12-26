# Changelog

All notable changes to MCP Translation Sync will be documented here.

## [Unreleased]

### Added
- 🔤 **Configurable key sorting**: `sortOrder` (asc/desc) and `sortCase` (sensitive/insensitive) options
- 🎛️ **Runtime dry-run override**: Chat can temporarily enable/disable dry-run mode without changing settings
- 🌐 **Locale-aware sorting**: Uses `Intl.Collator` for natural language key ordering with numeric support

### Changed
- 🏗️ **Command architecture refactoring**: Migrated to BaseCommand pattern with template method
  - Eliminated ~590 lines of duplicated boilerplate
  - Standardized workflow: validate → model selection → execute with progress
  - Improved testability with dependency injection
- 🔄 **Translation pipeline**: Extracted modular stages (categorize, translate, batch/concurrent)
- 🏗️ **Modular extension structure**: Code reorganized into commands/, chat/, services/, ui/ folders
- 📦 **Centralized constants**: All magic strings moved to `constants.ts`
- 🔧 **Standardized error handling**: Consistent `wrapError()` pattern across handlers
- ✂️ **Function decomposition**: Large functions split for readability

---

## [1.0.0] - 2025-11-22

### Core Features
- 🎉 **Production-ready release**
- 🤖 **GitHub Copilot integration** for AI-powered translations
- 🎯 **Smart translation reuse** - only translates when necessary
- 🔍 **Placeholder preservation** - validates 6 patterns (`{var}`, `{{var}}`, `%s`, etc.)
- 📝 **Git-based change detection** - 6 comparison strategies
- 💬 **Chat participant** `@translate` with natural language commands
- ⚡ **Cancellable operations** with real-time progress
- 🔤 **Automatic key sorting** - alphabetical ordering

### Commands
| Command | Description |
|---------|-------------|
| `MCP: Sync Translations` | Sync all enabled translation files |
| `MCP: Sync Specific Languages` | Interactive language picker |
| `MCP: Discover Translation Files` | Auto-find translation files |
| `MCP: Add Language` | Add new language file |
| `MCP: Clear Remembered Model` | Reset model preference |
| `MCP: View Settings` | Quick access to configuration |

### Chat Commands
```bash
@translate sync                    # Sync all languages
@translate /discover               # Auto-discover files
@translate sync only fr and de     # Specific languages
@translate sync except es          # Exclude languages
@translate sync staged changes     # Git strategy
@translate sync last 5 commits     # Historical changes
@translate sync with temp 0.8      # Runtime overrides
```

### Configuration
- 40+ settings for complete customization
- Git strategies: head-vs-working, head-vs-staged, last-n-commits, etc.
- All BCP-47 language tags supported (100+ languages)
- Model preferences with memory

### Documentation
- [User Guide](user-guide.md) - Complete usage docs
- [Configuration](configuration.md) - All settings explained
- [Git Strategies](git-strategies.md) - Change detection
- [FAQ](faq.md) - Troubleshooting

---

## Links

- [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=noble-wave.mcp-translation-sync)
- [GitHub Repository](https://github.com/noble-wave/mcp-translation-sync)
- [Report Issues](mailto:issue@noble-wave.com)
