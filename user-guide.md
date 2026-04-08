# User Guide

Complete guide to using MCP Translation Server in VS Code.

## Table of Contents

- [Installation](#installation)
- [First Sync](#first-sync)
- [Configuration](#configuration)
- [Daily Workflow](#daily-workflow)
- [Advanced Features](#advanced-features)
- [Troubleshooting](#troubleshooting)

## Installation

### Prerequisites

- **VS Code** 1.80.0 or later
- **GitHub Copilot** extension (for AI translations)
- **Node.js** 18+ (for development)
- **Git** (optional, for change detection)

### Install from Marketplace

1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions)
3. Search for **"MCP Translation Sync"**
4. Click **Install**
5. Reload window when prompted

### Verify Installation

1. Press `Ctrl+Shift+P`
2. Type "MCP:"
3. You should see:
   - `MCP: Sync Translations`
   - `MCP: Discover Translation Files`
   - `MCP: Check Translation Health`
   - `MCP: View Settings`
   - `MCP: Clear Remembered Model`

## First Sync

### Step 1: Open Your Project

Open a project with translation files. Common structures supported:

```
my-app/
  src/
    i18n/
      en.json    ← Source language file (source of truth)
      fr.json
      de.json
      es.json
```

### Step 2: Discover Translation Files

**Recommended**: Use auto-discovery to find all translation files automatically.

**Command Palette**:
1. Press `Ctrl+Shift+P`
2. Type **"MCP: Discover Translation Files"**
3. Extension scans your project and finds all translation files
4. Review the list and confirm
5. Configuration saved automatically to workspace settings

**Copilot Chat**:
```
@translate /discover
```

After discovery, click the **📝 View Settings** button to review and edit configuration.

The extension will:
- Find all JSON translation files in your project
- Detect language codes from filenames (e.g., `en.json`, `fr-FR.json`, `messages.de.json`)
- **Exclude common config files** (package.json, tsconfig.json, etc.)
- Identify your source language file
- **Preserve existing settings** (enabled flags, custom paths)
- Save merged configuration to `.vscode/settings.json`

**Re-discovery is Safe**: 
Running discovery again will:
- ✅ Add newly created translation files
- ✅ Remove files that no longer exist
- ✅ **Preserve your `enabled: false` settings**
- ✅ Keep manual path customizations

**Manual Configuration** (for advanced setups only):
See [Configuration Reference](configuration.md) for manual setup.

### Step 3: Run First Sync

**Command Palette**:
1. Press `Ctrl+Shift+P`
2. Type **"MCP: Sync Translations"**
3. Select your Copilot model (if prompted)
4. Watch the progress notification with real-time updates

**Copilot Chat**:
```
@translate sync
```

**Progress indicators show**:
- Current file being processed
- Translation progress (e.g., "5/12 keys")
- Real-time status updates
- Completion summary

**View detailed logs**: Output panel → "MCP Translation Sync"

### Step 5: Using Chat with Config Overrides

**Temporary Configuration Overrides**:

You can override settings temporarily in chat without changing workspace settings:

```
@translate sync with temperature 0.8
@translate sync only French and German
@translate sync except Spanish with batch size 50
@translate sync staged changes
@translate sync last 3 commits with retry 5 times
```

**Supported Overrides** (12 settings):
- **Temperature**: `temperature 0.8`, `more creative`, `deterministic`
- **Batch Settings**: `batch size 50`, `batch strategy concurrent`
- **Language Filter**: `only fr, de, es`, `except Spanish`, `skip Italian`
- **Git Strategy**: `staged changes`, `last 3 commits`, `uncommitted changes`
- **Retry Settings**: `retry 5 times`, `no retries`
- **Sorting**: `disable sorting`, `no sorting`
- **Dry Run**: `dry run`, `preview only`, `preview changes`
- **Custom Instructions**: `instructions: use formal tone`
- **Continue on Error**: `stop on error`, `continue on failure`

**How it Works**:
1. Copilot analyzes your prompt for config overrides
2. Shows detected overrides before sync (with confidence scores)
3. Applies overrides temporarily (not saved to settings)
4. Syncs with modified configuration
5. Next sync uses normal settings again

**Example Output**:
```
🎛️ Runtime Config Overrides:

🎚️ Model:
  • Temperature: 0.8

⚙️ Translation:
  • Batch Size: 50

🔄 Sync:
  • Git Strategy: "head-vs-staged"
```

### Step 4: Review Results

Open your translation files. You'll see:
- ✅ New keys added (from source language file)
- ✅ Removed keys deleted (not in source language file)
- ✅ Keys alphabetically sorted
- ✅ Placeholders preserved
- ✅ Existing valid translations kept
- ✅ Changed source values re-translated (git-detected)

## Configuration

### Essential Settings

```json
{
  // Core configuration
  "mcpTranslation.translation.projectRoot": ".",
  "mcpTranslation.translation.sourceLanguage": "en",
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/en.json", "language": "en", "enabled": true },
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/de.json", "language": "de", "enabled": true }
  ],
  
  // Model preferences
  "mcpTranslation.languageModel.preferredVendor": "copilot",
  "mcpTranslation.languageModel.rememberLastUsed": true,
  
  // Sync behavior
  "mcpTranslation.sync.dryRun": false,
  "mcpTranslation.sync.sortKeys": true,  // Sort both source and translations
  "mcpTranslation.sync.sortOrder": "asc",  // Ascending order
  "mcpTranslation.sync.sortCase": "insensitive",  // Case-insensitive
  "mcpTranslation.sync.detectSourceChanges": true,
  "mcpTranslation.sync.gitComparisonStrategy": "head-vs-working",
  
  // Translation options (defaults shown - batch-request is recommended)
  "mcpTranslation.translation.batchingStrategy": "batch-request",
  "mcpTranslation.translation.batchRequestSize": 20,
  "mcpTranslation.translation.todoMarker": "TODO:",
  "mcpTranslation.translation.customInstructions": "Use formal tone. Translate 'app' as 'application'."
}
```

### Common Configurations

#### Large Projects (50+ languages)
```json
{
  "mcpTranslation.translation.maxConcurrentTranslations": 10,
  "mcpTranslation.translation.retryAttempts": 5,
  "mcpTranslation.translation.batchingStrategy": "batch-request",
  "mcpTranslation.translation.batchRequestSize": 25
}
```
**Note**: Using `batch-request` strategy reduces API calls by ~95% for large updates, making it ideal for projects with many keys.

#### Preview Mode (Review Before Apply)
```json
{
  "mcpTranslation.sync.dryRun": true
}
```

#### Selective Translation - Specific Languages Only
```json
{
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/de.json", "language": "de", "enabled": true },
    { "path": "src/i18n/es.json", "language": "es", "enabled": true },
    { "path": "src/i18n/test.json", "language": "test", "enabled": false }
  ]
}
```

**Alternative - Use Commands**:
- `Ctrl+Shift+P` → `MCP: Sync Specific Languages` (interactive picker)
- Chat: `@translate sync only fr and de`
- Chat: `@translate sync except es`
- Chat: `@translate sync only French and German with temperature 0.5` (combined overrides)

**Advanced Chat Examples**:
```
@translate sync with batch size 30 and temperature 0.7
@translate sync only fr, de, es with retry 5 times
@translate sync staged changes, disable sorting
@translate sync last 5 commits with batch strategy concurrent
```

## Daily Workflow

### Scenario 1: Adding New Features

1. **Edit your source language file** - Add your new keys to `en.json` (or your source file):
   ```json
   {
     "welcome": "Welcome to our app",
     "newFeature": {
       "title": "New Feature",
       "description": "Check out what's new"
     }
   }
   ```

2. **Run Sync** - `Ctrl+Shift+P` → `MCP: Sync Translations`

3. **Commit All** - All translation files updated automatically:
   ```bash
   git add src/i18n/
   git commit -m "Add new feature translations"
   ```

### Scenario 2: Updating Existing Text

1. **Change source language value** in `en.json`:
   ```json
   {
     "greeting": "Hi there!"  // Was: "Hello"
   }
   ```

2. **Commit to git** (required for change detection):
   ```bash
   git add src/i18n/en.json
   git commit -m "Update greeting"
   ```

3. **Run Sync** - Only changed keys are re-translated

### Scenario 3: Using Copilot Chat

**Natural language commands in Copilot Chat:**

```
@translate /discover                      # Auto-find translation files
@translate /health                        # Check translation health
@translate                                # Sync all translations
@translate sync only fr and de            # Sync specific languages
@translate sync except es                 # Exclude specific languages
@translate sync comparing staged changes  # Use staged changes
@translate sync last 5 commits            # Check last N commits
@translate sync from v1.0.0 to v2.0.0     # Between commits
```

**Chat intelligently detects**:
- Git comparison strategies from keywords
- Language filters ("only", "except")
- Commit ranges and counts
- Natural language intent

### Scenario 4: Selective Translation

**Copilot Chat** (fastest):
```
@translate sync only fr and de
@translate sync only french and german
@translate sync except es and it
```

**Command Palette** (interactive):
1. `Ctrl+Shift+P` → `MCP: Sync Specific Languages`
2. Multi-select languages from picker
3. Press Enter to sync selected only

**Settings** (persistent configuration):
Modify `enabled` field in translation files:

```json
{
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/de.json", "language": "de", "enabled": true },
    { "path": "src/i18n/es.json", "language": "es", "enabled": false }
  ]
}
```

### Scenario 5: Health Check

**Check workspace health before syncing:**

**Command Palette**:
1. `Ctrl+Shift+P` → `MCP: Check Translation Health`
2. View interactive dashboard with:
   - Undiscovered translation files
   - Out-of-sync translations
   - Missing files
   - Incomplete translations (TODO markers)
   - Per-module health scores

**Copilot Chat**:
```
@translate /health
```

**Use cases**:
- Before syncing to see what needs attention
- After adding new features to verify completeness
- Regular health checks in CI/CD pipeline
- Finding undiscovered translation files

### Scenario 6: Preview Changes (Dry-Run)

1. **Enable dry-run** in settings:
   ```json
   { "mcpTranslation.sync.dryRun": true }
   ```
2. **Run sync**
3. **Review report**: `translation-sync-preview.md` in project root
4. **Apply changes**: Disable dry-run and sync again

## Advanced Features

### Key Ordering

**Alphabetical Sorting (Default)**

Both source and translation files are sorted alphabetically:

```json
{
  "mcpTranslation.sync.sortKeys": true  // Default
}
```

**Benefits:**
- ✅ Consistent structure across all files
- ✅ Easy to find keys
- ✅ Reduces merge conflicts
- ✅ Consistent structure between source and translations

**Preserve Original Order**

Keep your source file's original key order:

```json
{
  "mcpTranslation.sync.sortKeys": false
}
```

**Benefits:**
- ✅ Respects intentional organization (grouped by feature, module, priority)
- ✅ Translations mirror source file exactly
- ✅ No modification of source file structure

**Note:** With `sortKeys: false`, translations will always match your source file's key order exactly.

### Git-Based Change Detection

Automatically detects which source language values changed:

**Strategies**:
- `head-vs-working` (default) - All uncommitted changes
- `head-vs-index` - Only staged changes
- `index-vs-working` - Only unstaged changes
- `head-vs-head~N` - Last N commits
- `commit-range` - Between two specific commits

**Chat Detection**:
```
@translate sync staged changes          → head-vs-index
@translate since last commit            → head-vs-head~1
@translate last 5 commits               → head-vs-head~5
@translate from v1.0.0 to v2.0.0        → commit-range
```

See [git-strategies.md](git-strategies.md) for details.

### Placeholder Preservation

Automatically validates these patterns:
- `{name}` - Single braces
- `{{count}}` - Double braces
- `%s`, `%d` - Printf style
- `%(name)s` - Python style
- `${var}` - Template literals
- `[key]` - Brackets

**Example**:
```json
// en.json
{ "greeting": "Hello, {name}!" }

// fr.json - Valid
{ "greeting": "Bonjour, {name}!" }

// fr.json - Invalid (missing placeholder)
{ "greeting": "Bonjour!" }  // ❌ Triggers re-translation
```

### Force Re-translation

Prefix any value with `TODO:` to force re-translation:

```json
{
  "greeting": "TODO: needs better translation",
  "farewell": "Au revoir"
}
```

Next sync will re-translate `greeting`.

### Custom Translation Instructions

Append custom instructions to every translation prompt:

```json
{
  "mcpTranslation.translation.customInstructions": "Use formal tone. Translate 'app' as 'application'. Keep brand names like 'Acme Corp' in English."
}
```

**Use cases**:
- Specify tone/formality level
- Define terminology preferences
- Preserve brand names or technical terms
- Add domain-specific context

**Example customizations**:
```json
// Legal documents
{ "customInstructions": "Use formal legal language. Maintain precision and avoid contractions." }

// Marketing content
{ "customInstructions": "Use friendly, conversational tone. Keep it engaging and upbeat." }

// Technical documentation
{ "customInstructions": "Keep technical terms in English. Use clear, concise language." }
```

### Batching Strategy Selection

**Default: Batch Request (Recommended)** ⭐

The extension uses **batch-request** strategy by default for optimal performance:

```json
{
  "mcpTranslation.translation.batchingStrategy": "batch-request",
  "mcpTranslation.translation.batchRequestSize": 20
}
```

**Benefits of batch-request (default)**:
- ✅ **95% fewer API calls** - dramatically faster
- ✅ **Lower latency** - less network overhead
- ✅ **Better for rate limits** - consolidates requests
- ✅ **Works great for most projects** - handles typical workflows efficiently

**How it works**: Groups multiple translation keys into a single LLM request, processes them together, returns all translations at once.

---

**Alternative: Concurrent Strategy** (for edge cases)

Switch to concurrent only if you encounter specific issues:

```json
{
  "mcpTranslation.translation.batchingStrategy": "concurrent",
  "mcpTranslation.translation.maxConcurrentTranslations": 5
}
```

**When to use concurrent**:
- ❗ Batch requests fail consistently (JSON parsing errors)
- ❗ Need per-key retry control for unreliable networks  
- ❗ Extremely long individual texts exceeding token limits
- ❗ Troubleshooting translation issues (isolate problem keys)

**How it works**: Sends individual API calls for each key, processes them concurrently (default 5 at a time), retries failed keys independently.

---

**Performance Comparison** (100 keys):

| Strategy | API Calls | Typical Speed | Retry Granularity | Best For |
|----------|-----------|---------------|-------------------|----------|
| **batch-request** ⭐ | 5 calls | ~10-15 sec | Per batch | **Most projects** (default) |
| **concurrent** | 100 calls | ~30-45 sec | Per key | Edge cases, troubleshooting |

**Recommendation**: Keep the default `batch-request` unless you have a specific reason to change it. The concurrent strategy is maintained for special cases and backward compatibility.

**Adjusting batch size**:

If using batch-request with very long texts:
```json
{
  "mcpTranslation.translation.batchRequestSize": 10  // Reduce if hitting token limits
}
```

Valid range: 1-100 (default: 20 works well for most projects)

### Model Selection

**First time**: Choose your preferred Copilot model

**Remember preference**:
```json
{
  "mcpTranslation.languageModel.preferredVendor": "copilot",
  "mcpTranslation.languageModel.preferredFamily": "gpt-4",
  "mcpTranslation.languageModel.rememberLastUsed": true
}
```

**Change model**: `Ctrl+Shift+P` → `MCP: Clear Remembered Model`

### Backup Before Sync

Enable automatic backups:

```json
{
  "mcpTranslation.sync.backupBeforeSync": true,
  "mcpTranslation.sync.backupPath": "./.backups"
}
```

Backups saved with timestamp: `.backups/2025-11-21T10-30-00_fr.json`

### Cancellation Support

Long sync? Click the **Cancel** button in the progress notification.

Sync will stop gracefully at the next safe point.

## Troubleshooting

### "No Copilot models available"

**Cause**: GitHub Copilot not active

**Solution**:
1. Install GitHub Copilot extension
2. Sign in to GitHub Copilot
3. Check Copilot icon in status bar is active
4. Try syncing again

### "File not found" or "No source language file"

**Cause**: Translation files not discovered or source language mismatch

**Solution**:
1. **Run discovery**: `Ctrl+Shift+P` → `MCP: Discover Translation Files` or `@translate /discover`
2. Verify source language setting matches an actual file:
   - If `sourceLanguage: "en"`, ensure you have a file with `language: "en"`
3. Check discovered files in settings:
   - `Ctrl+,` → Search "MCP Translation" → View `translation.files` array
4. Ensure source file has `enabled: true`
5. If using custom setup, verify paths are relative to `projectRoot`

### Translations look wrong / not natural

**Cause**: Model selection or prompt issue

**Solution**:
1. Try different Copilot model (GPT-4 vs GPT-3.5)
2. Check placeholders are preserved
3. Ensure `en.json` has clear, complete sentences
4. Use context-rich keys: `button.save.label` vs `btn_sv`

### Sync is slow

**Cause**: Many languages or large files

**Solution**:
1. **Increase concurrency**:
   ```json
   { "mcpTranslation.translation.maxConcurrentTranslations": 10 }
   ```
2. **Sync specific languages** (recommended):
   - Command: `Ctrl+Shift+P` → `MCP: Sync Specific Languages`
   - Chat: `@translate sync only fr and de`
   - Or disable in settings: set `enabled: false` for unused languages
3. **Enable git change detection** (only translates changed keys):
   ```json
   { "mcpTranslation.sync.detectSourceChanges": true }
   ```
4. **Use dry-run first** to preview changes before committing

### Git strategy not working

**Cause**: Not in git repository or `en.json` not tracked

**Solution**:
1. Initialize git: `git init`
2. Add and commit `en.json`:
   ```bash
   git add src/i18n/en.json
   git commit -m "Add translations"
   ```
3. Make changes and sync

### Placeholders getting corrupted

**Cause**: Placeholder pattern not detected

**Solution**:
1. Use standard patterns: `{var}`, `{{var}}`, `%s`, `${var}`
2. Review translation output logs
3. Use TODO marker to force re-translation with correct placeholders

### Dry-run report not generated

**Cause**: Dry-run not enabled or no changes detected

**Solution**:
1. Verify `mcpTranslation.sync.dryRun: true` in settings
2. Make changes to `en.json`
3. Run sync
4. Check project root for `translation-sync-preview.md`

## Best Practices

### 1. Commit Source Language File First
Always commit source language changes before syncing for accurate change detection.

### 2. Use Meaningful Keys
```json
// ❌ Bad
{ "btn1": "Save", "txt2": "Cancel" }

// ✅ Good
{ "button.save": "Save", "button.cancel": "Cancel" }
```

### 3. Keep Sentences Complete
```json
// ❌ Bad (AI can't translate context)
{ "click": "Click", "here": "here" }

// ✅ Good
{ "action.clickHere": "Click here to continue" }
```

### 4. Use Placeholders Consistently
```json
// ✅ Consistent pattern
{
  "greeting": "Hello, {name}!",
  "farewell": "Goodbye, {name}!",
  "welcome": "Welcome, {name}!"
}
```

### 5. Review Translations Periodically
Even AI makes mistakes. Review and fix when needed, then sync will preserve your corrections.

### 6. Use Dry-Run for Large Changes
When restructuring translations, run dry-run first to preview the impact.

## Getting Help

- **View Logs**: Output panel → "MCP Translation Sync"
- **Check Settings**: Press `Ctrl+,` → Search "MCP"
- **Report Issues**: [Open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new) on our public documentation repository
- **Documentation**: Browse [online docs](https://mcp-translation-sync.noble-wave.com) or `/docs` folder
- **Email Support**: [mcp@noble-wave.com](mailto:mcp@noble-wave.com)

---

**Next**: [Configuration Reference](configuration.md) | [Git Strategies](git-strategies.md)
