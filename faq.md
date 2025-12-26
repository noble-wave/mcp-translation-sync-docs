# FAQ - Frequently Asked Questions

## General Questions

### What is MCP Translation Sync?

A VS Code extension and standalone server that automatically synchronizes translation files using GitHub Copilot's AI. It keeps your source language file (typically `en.json`) as the source of truth and intelligently updates all other language files.

### Do I need GitHub Copilot?

**For VS Code Extension**: Yes, GitHub Copilot is required for AI-powered translations.

**For Standalone Server**: No, it uses mock translations (`[LANG] text` format) and can be extended with any translation API.

### Is this free?

The extension itself is free and open-source (MIT license). However, you need:
- GitHub Copilot free or subscription for AI translations
- Or implement your own translation API (DeepL, Google Translate, etc.)

### What languages are supported?

**All valid BCP-47 language tags** via `Intl.Locale` validation (100+ languages).

Common examples:
- Simple codes: English (`en`), French (`fr`), German (`de`), Spanish (`es`), Italian (`it`), Portuguese (`pt`), Russian (`ru`), Japanese (`ja`), Chinese (`zh`), Korean (`ko`), Arabic (`ar`), Hindi (`hi`), Dutch (`nl`), Polish (`pl`), Turkish (`tr`), Swedish (`sv`), Norwegian (`no`), Danish (`da`), Finnish (`fi`)
- Regional variants: `en-US`, `en-GB`, `pt-BR`, `zh-CN`, `fr-CA`, `es-MX`
- Script variants: `zh-Hans` (Simplified), `zh-Hant` (Traditional), `sr-Cyrl` (Cyrillic), `sr-Latn` (Latin)
- Any IANA-registered language tag

The extension uses `Intl.Locale` for validation, automatically supporting all current and future BCP-47 language tags.

Full list in `src/util/languages.ts`.

## Installation & Setup

### How do I install it?

1. Open VS Code
2. Press `Ctrl+Shift+X` (Extensions)
3. Search "MCP Translation Sync"
4. Click Install

### How do I configure it?

**Recommended**: Use auto-discovery
1. Press `Ctrl+Shift+P` → `MCP: Discover Translation Files`
2. Or in Copilot Chat: `@translate /discover`
3. Extension auto-detects and configures everything

**Manual** (advanced only): Press `Ctrl+,` → Search "MCP Translation" → Configure settings manually

Or use: `Ctrl+Shift+P` → `MCP: View Settings`

### How do I view and edit all settings?

**Command Palette**: `Ctrl+Shift+P` → `MCP: View Settings`

**Chat**: `@translate /settings` or `@translate /config`

Opens VS Code settings UI filtered to show only MCP Translation extension settings.

### Why aren't some .json files detected as translation files?

Discovery **excludes common config files** to prevent false positives:
- Configuration: `package.json`, `tsconfig.json`, `tsconfig.app.json`, `angular.json`, `vite.config.json`, etc.
- Linter/formatter: `.eslintrc.json`, `.prettierrc.json`, `tslint.json`
- Package managers: `composer.json`, `bower.json`, `manifest.json`

Only files with **valid language codes** in filenames are detected (e.g., `en.json`, `fr-FR.json`, `messages.de.json`).

If a translation file is being excluded incorrectly, you can add it manually in settings:
```json
{
  "mcpTranslation.translation.files": [
    { "path": "custom-name.json", "language": "en", "enabled": true }
  ]
}
```

### Can I use it without VS Code?

Yes! Run the standalone server:

The extension runs within VS Code and provides commands and chat integration for translation management.

## Usage Questions

### How do I sync translations?

**Copilot Chat** (fastest):
```
@translate
@translate sync
@translate sync only fr and de
```

**Command Palette**:
1. Press `Ctrl+Shift+P`
2. Type "MCP: Sync Translations"
3. Select model if prompted

### How do I check translation health?

**Copilot Chat**:
```
@translate /health
```

**Command Palette**:
1. Press `Ctrl+Shift+P`
2. Type "MCP: Check Translation Health"
3. View interactive dashboard or chat summary

**What it checks**:
- Undiscovered translation files
- Out-of-sync translations
- Missing translation files
- Incomplete translations (TODO markers)
- Per-module health scores

### What happens during sync?

1. Reads source language file as source of truth
2. Detects changes (new/removed/modified keys)
3. Compares with each language file
4. Reuses valid existing translations
5. Translates only what's needed (new keys, changed source language, TODO markers, placeholder mismatches)
6. Writes updated files with sorted keys

### How do I force re-translation?

Prefix the value with `TODO:`:

```json
{
  "greeting": "TODO: improve this translation"
}
```

Next sync will re-translate it.

### Can I preview changes before applying?

Yes! Enable dry-run mode:

```json
{ "mcpTranslation.sync.dryRun": true }
```

Run sync → Review `translation-sync-preview.md` → Disable dry-run → Sync again

### How do I cancel a long-running sync?

Click the **Cancel** button in the progress notification. Sync stops gracefully at the next safe point.

## Git Integration

### Do I need git?

No, but highly recommended. Git-based change detection makes syncs smarter by only re-translating keys where English actually changed.

### How does git change detection work?

Compares your current source language file with the committed version to detect which keys changed. Only those keys are re-translated.

**Example**:
```bash
# Committed
{ "greeting": "Hello" }

# Current
{ "greeting": "Hi there" }

# Sync detects "greeting" changed → re-translates only that key
```

### What if I'm not using git?

Sync still works! Falls back to TODO marker and placeholder mismatch detection.

### What are git strategies?

Different ways to detect changes:
- `head-vs-working` - All uncommitted changes (default)
- `head-vs-index` - Only staged changes
- `index-vs-working` - Only unstaged changes
- `head-vs-head~N` - Last N commits
- `commit-range` - Between specific commits

See [git-strategies.md](git-strategies.md) for details.

### Can I auto-detect strategy from chat?

Yes! Chat automatically detects:

```
@translate sync staged            → head-vs-index
@translate last 5 commits         → head-vs-head~5
@translate from v1.0.0 to v2.0.0  → commit-range
```

## Translation Behavior

### When are translations reused?

When ALL these conditions are true:
- ✅ Translation exists and is not empty
- ✅ Doesn't start with `TODO:`
- ✅ Source language value hasn't changed (git-detected)
- ✅ Placeholders match

### When are translations regenerated?

When ANY of these is true:
- 🔄 Key is new
- 🔄 Source language value changed (git)
- 🔄 Starts with `TODO:`
- 🔄 Placeholder mismatch

### What are placeholders?

Variable tokens like:
- `{name}` - Single braces
- `{{count}}` - Double braces
- `%s`, `%d` - Printf style
- `${var}` - Template literals

They must be preserved exactly in translations.

### What if placeholders don't match?

Key is marked for re-translation to fix the mismatch.

**Example**:
```json
// en.json
{ "msg": "Hello, {name}!" }

// fr.json - Missing placeholder
{ "msg": "Bonjour!" }  // ❌ Triggers re-translation

// fr.json - Correct
{ "msg": "Bonjour, {name}!" }  // ✅ Reused
```

## Model Selection

### How do I choose a Copilot model?

First sync prompts you to select. Your choice is remembered.

**Change model**: `Ctrl+Shift+P` → `MCP: Clear Remembered Model`

### Can I set a preferred model?

Yes:

```json
{
  "mcpTranslation.languageModel.preferredVendor": "copilot",
  "mcpTranslation.languageModel.preferredFamily": "gpt-4"
}
```

### Which model is best?

- **GPT-4** - Higher quality, slower, more expensive
- **GPT-3.5** - Faster, cheaper, good quality

For most use cases, GPT-4 recommended.

### Does it remember my model choice?

Yes, if `rememberLastUsed` is enabled (default):

```json
{ "mcpTranslation.languageModel.rememberLastUsed": true }
```

## Performance

### Sync is taking too long

**Solutions**:

1. **Increase concurrency**:
   ```json
   { "mcpTranslation.translation.maxConcurrentTranslations": 10 }
   ```

2. **Sync specific languages**:
   ```json
   { "mcpTranslation.translation.targetLanguages": ["fr", "de"] }
   ```

3. **Enable git change detection** (only translates changed keys):
   ```json
   { "mcpTranslation.sync.detectSourceChanges": true }
   ```

### Can I sync specific languages only?

Yes, three ways:

**1. Copilot Chat**:
```
@translate sync only fr and de
@translate sync except test
```

**2. Interactive Command**:
`Ctrl+Shift+P` → `MCP: Sync Specific Languages` → Select from list

**3. Settings** (persistent):
Set `enabled: false` for languages you want to skip:
```json
{
  "mcpTranslation.translation.files": [
    { "path": "src/i18n/fr.json", "language": "fr", "enabled": true },
    { "path": "src/i18n/test.json", "language": "test", "enabled": false }
  ]
}
```

### How many API calls does sync make?

**Depends on**:
- Number of new/changed keys
- Number of target languages
- Translation reuse (existing valid translations are NOT re-translated)

**Example**:
- 5 new keys × 10 languages = 50 API calls
- But if 50 keys unchanged = 0 API calls (reused)

## Troubleshooting

### "No Copilot models available"

**Solutions**:
1. Install GitHub Copilot extension
2. Sign in to GitHub Copilot
3. Check Copilot status bar icon is active
4. Restart VS Code

### "File not found" errors

**Solutions**:
1. **Run discovery**: `Ctrl+Shift+P` → `MCP: Discover Translation Files`
2. Verify `translation.sourceLanguage` matches an actual file's language code
3. Check `.vscode/settings.json` for `translation.files` array
4. Ensure paths are relative to `translation.projectRoot`

### Extension not activating

**Solutions**:
1. Reload window: `Ctrl+Shift+P` → "Developer: Reload Window"
2. Check VS Code version ≥ 1.80.0
3. View logs: Output panel → "MCP Translation Sync"
4. Check extension is enabled in Extensions view

### Translations look wrong

**Solutions**:
1. Try different model (GPT-4 vs GPT-3.5)
2. Check source language is clear and complete
3. Ensure placeholders are correct
4. Review logs for errors

### Git strategy not working

**Solutions**:
1. Verify git repo exists: `git status`
2. Commit source language file: `git add en.json && git commit -m "Add translations"`
3. Check git executable in PATH
4. Review Output panel for git command errors

### Sync fails with error

**Solutions**:
1. Check Output panel for detailed error
2. Enable debug logging:
   ```json
   { "mcpTranslation.logging.level": "debug" }
   ```
3. Try with single language first
4. Check GitHub Copilot status
5. Disable and re-enable extension

### Placeholders getting corrupted

**Solutions**:
1. Use standard formats: `{var}`, `{{var}}`, `%s`
2. Check placeholder patterns match your format
3. Force re-translation with TODO marker
4. Review translation manually and commit correction

## Configuration

### Where do I put configuration?

**VS Code Extension**: Settings UI or `.vscode/settings.json`

**Standalone Server**: `.env` file or environment variables

### Can I have project-specific settings?

Yes! Add to `.vscode/settings.json` in your project:

```json
{
  "mcpTranslation.projectRoot": ".",
  "mcpTranslation.sourceLanguagePath": "locales/en.json",
  "mcpTranslation.translation.targetLanguages": ["fr", "de"]
}
```

### What's the default config?

See [configuration.md](configuration.md) for all defaults.

Key defaults:
- `projectRoot`: `.`
- `sourceLanguagePath`: `src/assets/i18n/en.json`
- `sortKeys`: `true`
- `gitComparisonStrategy`: `head-vs-working`

## Advanced

### Can I use my own translation API?

Yes! Edit `src/tools/translate.ts`:

```typescript
export async function translateTextHandler(input, model?, config?) {
  // Replace mock translator with:
  const response = await fetch('https://api.deepl.com/v2/translate', {
    // ... your API call
  });
  return { translated: response.translation };
}
```

### Can I integrate with CI/CD?

The extension is designed for interactive VS Code use. For CI/CD integration, consider:
- Running sync as part of your build process via VS Code tasks
- Using git hooks to trigger syncs before commits
- Implementing custom automation scripts that leverage your preferred translation API

### Can I customize sorting?

Keys are always sorted alphabetically. To disable:

```json
{ "mcpTranslation.sync.sortKeys": false }
```

### Can I sync on save?

Not built-in, but you can use VS Code tasks:

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [{
    "label": "Sync Translations",
    "type": "shell",
    "command": "${command:mcpTranslation.syncTranslations}",
    "runOn": "folderOpen"
  }]
}
```

## Support

### Where do I get help?

- **Documentation**: Browse [online docs](https://mcp-translation-sync.noble-wave.com) or `/docs` folder
- **Output Logs**: View → Output → "MCP Translation Sync"
- **Report Issues**: [Open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new) on our public documentation repository
- **Email Support**: [mcp@noble-wave.com](mailto:mcp@noble-wave.com)

### How do I report a bug?

1. Open [an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new) on our public documentation repository
2. Include:
   - VS Code version
   - Extension version
   - Output panel logs
   - Steps to reproduce
   - Expected vs actual behavior

Alternatively, email details to [mcp@noble-wave.com](mailto:mcp@noble-wave.com)

### Can I contribute?

Yes! See [development.md](development.md) for contribution guidelines.

### Is there a roadmap?

Check [GitHub Issues](https://github.com/noble-wave/mcp-translation-sync-docs/issues) for planned features, discussions, and feature requests.

---

**Still have questions?** Visit our [documentation](https://mcp-translation-sync.noble-wave.com) or [open an issue](https://github.com/noble-wave/mcp-translation-sync-docs/issues/new)
