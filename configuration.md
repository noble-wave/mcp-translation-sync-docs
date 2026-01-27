# Configuration Reference

Complete reference for all configuration options.

## Configuration Methods

### 1. Auto-Discovery (Recommended)

Run `MCP: Discover Translation Files` command to auto-configure:
- Finds all translation files automatically
- Detects language codes from filenames (supports BCP-47 tags like `en`, `fr-FR`, `zh-Hans`)
- **Excludes common config files** (package.json, tsconfig.json, angular.json, etc.)
- Validates language codes using standard Intl.Locale API
- Identifies source language file
- Saves to `.vscode/settings.json`

**Copilot Chat**:
```
@translate /discover
```

### 2. Manual Settings

Press `Ctrl+,` and search for "MCP Translation"

Or edit `.vscode/settings.json`:

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

### Configuration Priority

1. **Runtime parameters** (chat-detected strategies, command options)
2. **VS Code settings** (`.vscode/settings.json` or user settings)
3. **Defaults** (built-in fallbacks)

## Settings Reference

### Translation Files

#### `mcpTranslation.translation.projectRoot`
**Type**: `string`  
**Default**: `"."`  
**Description**: Root directory containing translation files

**Example**:
```json
{ "mcpTranslation.translation.projectRoot": "." }
{ "mcpTranslation.translation.projectRoot": "./frontend" }
```

#### `mcpTranslation.translation.sourceLanguage`
**Type**: `string`  
**Default**: `"en"`  
**Description**: Language code of your source language file (must match a file's `language` field)

**Example**:
```json
{ "mcpTranslation.translation.sourceLanguage": "en" }
```

#### `mcpTranslation.translation.files`
**Type**: `array<TranslationFileConfig>`  
**Default**: `[]`  
**Description**: List of all translation files (auto-populated by discovery command)

**Structure**:
```typescript
interface TranslationFileConfig {
  path: string;      // Relative to projectRoot
  language: string;  // ISO language code (en, fr, de, etc.)
  enabled: boolean;  // Whether to sync this file
}
```

**Example**:
```json
{
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/en.json", "language": "en", "enabled": true },
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/de.json", "language": "de", "enabled": true },
    { "path": "src/i18n/test.json", "language": "test", "enabled": false }
  ]
}
```

**Best Practice**: Use `MCP: Discover Translation Files` command to auto-populate.

---

### Translation Options

#### `mcpTranslation.translation.todoMarker`
**Type**: `string`  
**Default**: `"TODO:"`  
**Description**: Marker prefix to force re-translation

**Example**:
```json
{
  "greeting": "TODO: needs better wording"
}
```

#### `mcpTranslation.translation.customInstructions`
**Type**: `string`  
**Default**: `""`  
**Description**: Custom instructions to append to translation prompts. Use this to specify tone, formality, terminology preferences, or any other translation-specific requirements.

**Example**:
```json
{
  "mcpTranslation.translation.customInstructions": "Use formal tone. Translate 'app' as 'application'. Keep brand names in English."
}
```

#### `mcpTranslation.translation.maxConcurrentTranslations`
**Type**: `number`  
**Default**: `5`  
**Description**: Maximum parallel translation requests (concurrent strategy only)

**Example**:
```json
{ "mcpTranslation.translation.maxConcurrentTranslations": 10 }
```

#### `mcpTranslation.translation.batchingStrategy`
**Type**: `string`  
**Default**: `"batch-request"` ⭐ **RECOMMENDED**  
**Options**: `"batch-request"`, `"concurrent"`  
**Description**: Translation batching strategy

- **batch-request** (default, recommended): Single API call with array of texts processed together by LLM. **95% fewer API calls**, significantly faster for most projects. Best for typical workflows with 5+ keys.
- **concurrent**: Multiple individual API calls processed concurrently with Promise.allSettled. Use only for edge cases requiring fine-grained per-key retry control or when batch mode encounters issues.

**Recommended**: Keep default unless you experience specific issues.

**Example**:
```json
{ "mcpTranslation.translation.batchingStrategy": "batch-request" }
```

**When to use concurrent** (rare cases only):
- Batch requests consistently fail with JSON parsing errors
- Need per-key retry granularity for unreliable networks
- Working with extremely long texts that exceed token limits even with small batch sizes

#### `mcpTranslation.translation.batchRequestSize`
**Type**: `number`  
**Default**: `20`  
**Range**: `1-100`  
**Description**: Number of keys to include in each batch request when using 'batch-request' strategy. Adjust based on text length to avoid token limits.

**Example**:
```json
{ "mcpTranslation.translation.batchRequestSize": 30 }
```

#### `mcpTranslation.translation.retryAttempts`
**Type**: `number`  
**Default**: `3`  
**Description**: Retry count for failed translations

#### `mcpTranslation.translation.retryDelayMs`
**Type**: `number`  
**Default**: `1000`  
**Description**: Delay between retry attempts (milliseconds)

---

### Sync Options

#### `mcpTranslation.sync.sortKeys`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Alphabetically sort all keys recursively

#### `mcpTranslation.sync.sortOrder`
**Type**: `string`  
**Default**: `"asc"`  
**Options**: `"asc"`, `"desc"`  
**Description**: Sort order for translation keys (ascending or descending)

#### `mcpTranslation.sync.sortCase`
**Type**: `string`  
**Default**: `"insensitive"`  
**Options**: `"sensitive"`, `"insensitive"`  
**Description**: Case sensitivity for key sorting. Insensitive treats `A` and `a` as equal.

#### `mcpTranslation.sync.indentSpaces`
**Type**: `number`  
**Default**: `2`  
**Description**: JSON file indentation

#### `mcpTranslation.sync.sortKeys`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Sort keys alphabetically in both source and translation files

**When `true` (default):**
- Source file is sorted alphabetically and saved
- Translation files are sorted alphabetically to match
- All files have consistent, predictable structure
- Easier to find keys and reduce merge conflicts

**When `false`:**
- Source file is NOT modified
- Translation files mirror source file's exact key order
- Respects intentional organization (by feature, module, priority, etc.)
- Suitable for teams with custom key organization strategies

**Example:**
```json
{
  // Default: Sort everything alphabetically
  "mcpTranslation.sync.sortKeys": true,
  
  // OR: Preserve source file order
  "mcpTranslation.sync.sortKeys": false
}
```

#### `mcpTranslation.sync.detectSourceChanges`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Use git to detect source language value changes

#### `mcpTranslation.sync.gitComparisonStrategy`
**Type**: `string`  
**Default**: `"head-vs-working"`  
**Options**: `"head-vs-working"`, `"head-vs-index"`, `"index-vs-working"`, `"head-vs-head~N"`, `"commit-range"`, `"manual-diff"`  
**Description**: Git comparison strategy for change detection

See [git-strategies.md](git-strategies.md) for details.

#### `mcpTranslation.sync.gitCommitCount`
**Type**: `number`  
**Default**: `2`  
**Description**: Number of commits to compare (for `head-vs-head~N` strategy)

#### `mcpTranslation.sync.backupBeforeSync`
**Type**: `boolean`  
**Default**: `false`  
**Description**: Create backup files before syncing

#### `mcpTranslation.sync.backupPath`
**Type**: `string`  
**Default**: `"./.backups"`  
**Description**: Directory for backup files

#### `mcpTranslation.sync.dryRun`
**Type**: `boolean`  
**Default**: `false`  
**Description**: Preview changes without writing files

**Output**: Generates `translation-sync-preview.md` report

#### `mcpTranslation.sync.continueOnError`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Continue sync if translation fails (uses TODO marker fallback)

#### `mcpTranslation.sync.prettify`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Format JSON output with proper indentation

---

### Language Model Options

#### `mcpTranslation.languageModel.preferredVendor`
**Type**: `string`  
**Default**: `""`  
**Options**: `"copilot"`, `""` (any)  
**Description**: Preferred Copilot vendor

#### `mcpTranslation.languageModel.preferredFamily`
**Type**: `string`  
**Default**: `""`  
**Options**: `"gpt-4"`, `"gpt-3.5-turbo"`, `""` (any)  
**Description**: Preferred model family

#### `mcpTranslation.languageModel.rememberLastUsed`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Remember model selection between syncs

#### `mcpTranslation.languageModel.showModelChangeOption`
**Type**: `boolean`  
**Default**: `true`  
**Description**: Show "Change Model" button when using preferred model

#### `mcpTranslation.languageModel.temperature`
**Type**: `number`  
**Default**: `0.3`  
**Range**: `0.0 - 2.0`  
**Description**: LLM creativity (lower = more consistent)

#### `mcpTranslation.languageModel.maxTokens`
**Type**: `number`  
**Default**: `1000`  
**Description**: Maximum tokens per translation request

---

### Logging Options

#### `mcpTranslation.logging.level`
**Type**: `string`  
**Default**: `"info"`  
**Options**: `"debug"`, `"info"`, `"warn"`, `"error"`  
**Description**: Logging verbosity level

#### `mcpTranslation.logging.toFile`
**Type**: `boolean`  
**Default**: `false`  
**Description**: Write logs to file

#### `mcpTranslation.logging.filePath`
**Type**: `string`  
**Default**: `"./logs/mcp-translation.log"`  
**Description**: Log file path

#### `mcpTranslation.logging.maxFileSize`
**Type**: `string`  
**Default**: `"10m"`  
**Description**: Maximum log file size before rotation

#### `mcpTranslation.logging.maxFiles`
**Type**: `number`  
**Default**: `5`  
**Description**: Maximum number of rotated log files

---

## Configuration Examples

### Minimal Setup (Auto-Discovery)
```json
{
  "mcpTranslation.translation.projectRoot": ".",
  "mcpTranslation.translation.sourceLanguage": "en"
}
```
Then run: `MCP: Discover Translation Files` to auto-populate `translation.files`.

### Production Setup
```json
{
  "mcpTranslation.translation.projectRoot": ".",
  "mcpTranslation.translation.sourceLanguage": "en",
  "mcpTranslation.sync.sortKeys": true,
  "mcpTranslation.sync.sortOrder": "asc",
  "mcpTranslation.sync.sortCase": "insensitive",
  "mcpTranslation.sync.backupBeforeSync": true,
  "mcpTranslation.sync.continueOnError": false,
  "mcpTranslation.sync.detectSourceChanges": true,
  "mcpTranslation.logging.level": "info",
  "mcpTranslation.logging.toFile": true,
  "mcpTranslation.languageModel.rememberLastUsed": true
}
```

### Large Project Setup (Performance Optimized)
```json
{
  "mcpTranslation.translation.maxConcurrentTranslations": 15,
  "mcpTranslation.translation.retryAttempts": 5,
  "mcpTranslation.sync.gitComparisonStrategy": "head-vs-working",
  "mcpTranslation.sync.detectSourceChanges": true
}
```

### Preview/Testing Setup
```json
{
  "mcpTranslation.sync.dryRun": true,
  "mcpTranslation.sync.backupBeforeSync": true,
  "mcpTranslation.logging.level": "debug"
}
```

### Selective Languages
Use `enabled` field in translation files (auto-configured by discovery):
```json
{
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/en.json", "language": "en", "enabled": true },
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/de.json", "language": "de", "enabled": true },
    { "path": "src/i18n/es.json", "language": "es", "enabled": false },
    { "path": "src/i18n/test.json", "language": "test", "enabled": false }
  ]
}
```
Or use interactive command: `MCP: Sync Specific Languages`

---

**Next**: [Git Strategies](git-strategies.md) | [User Guide](user-guide.md)
