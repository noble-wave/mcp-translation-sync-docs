# Git Comparison Strategies

## Overview

The MCP Translation Server now supports flexible git comparison strategies to detect source language value changes. This allows you to customize **when** and **how** translations should be re-triggered based on your development workflow.

## Does it Catch Value-Only Changes?

**YES! ✅** The system detects **all** types of changes:

1. **Value changes** (same key, different value):
   ```json
   // Before:
   { "greeting": "Hello" }
   
   // After:
   { "greeting": "Hi there" }
   
   // ✅ Detected: "greeting" key will be re-translated
   ```

2. **New keys**:
   ```json
   // Before:
   { "greeting": "Hello" }
   
   // After:
   { "greeting": "Hello", "farewell": "Goodbye" }
   
   // ✅ Detected: "farewell" key added
   ```

3. **Removed keys**:
   ```json
   // Before:
   { "greeting": "Hello", "farewell": "Goodbye" }
   
   // After:
   { "greeting": "Hello" }
   
   // ✅ Detected: "farewell" key removed
   ```

4. **Nested value changes**:
   ```json
   // Before:
   { "auth": { "login": "Log in" } }
   
   // After:
   { "auth": { "login": "Sign in" } }
   
   // ✅ Detected: "auth.login" value changed
   ```

## Available Strategies

### 1. `head-vs-working` (Default)

Compares HEAD (last commit) with current working tree (includes staged + unstaged changes).

**Use when:** You want to detect all uncommitted changes.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'head-vs-working' 
});
```

**Example:**
```bash
# You have:
# - Committed: "greeting": "Hello"
# - Staged: "greeting": "Hi"
# - Working: "farewell": "Goodbye" (unstaged)

# ✅ Detects: Both "greeting" and "farewell"
```

---

### 2. `head-vs-staged`

Compares HEAD with staged changes only (what will be committed next).

**Use when:** You want to preview what translations will change on next commit.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'head-vs-staged' 
});
```

**Example:**
```bash
# You have:
# - Committed: "greeting": "Hello"
# - Staged: "greeting": "Hi"
# - Working: "farewell": "Goodbye" (unstaged)

# ✅ Detects: Only "greeting" (staged change)
```

---

### 3. `head-vs-unstaged`

Compares staged with working tree (unstaged changes only).

**Use when:** You want to see what changed since last `git add`.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'head-vs-unstaged' 
});
```

**Example:**
```bash
# You have:
# - Committed: "greeting": "Hello"
# - Staged: "greeting": "Hi"
# - Working: "greeting": "Hi", "farewell": "Goodbye"

# ✅ Detects: Only "farewell" (unstaged change)
```

---

### 4. `last-commit-vs-working`

Compares previous commit (HEAD~1) with current state.

**Use when:** You want to see changes since your last commit.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'last-commit-vs-working' 
});
```

**Example:**
```bash
git log --oneline
abc1234 Update greeting
def5678 Initial commit

# Compares def5678 (HEAD~1) vs current working tree
# Shows all changes made in abc1234 + uncommitted changes
```

---

### 5. `last-n-commits`

Compares N commits ago with current state.

**Use when:** You want to catch changes from multiple recent commits.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'last-n-commits',
  commitCount: 5  // Check last 5 commits
});
```

**Example:**
```bash
# You have 5 commits:
# HEAD:   Add "v5"
# HEAD~1: Add "v4"
# HEAD~2: Add "v3"
# HEAD~3: Add "v2"
# HEAD~4: Add "v1"
# HEAD~5: Initial

# Compares HEAD~5 vs current
# ✅ Detects: v1, v2, v3, v4, v5 + any uncommitted changes
```

---

### 6. `commit-range`

Compares between two specific commits.

**Use when:** You need precise control over comparison range.

```typescript
getChangedSourceKeys(projectRoot, sourceLanguagePath, { 
  strategy: 'commit-range',
  fromCommit: 'v1.0.0',  // Tag, hash, or ref
  toCommit: 'HEAD'       // Current commit
});
```

**Example:**
```bash
# Compare release v1.0.0 to current:
git tag
v1.0.0
v1.1.0

# Detects all changes between v1.0.0 and HEAD
# Useful for: release notes, migration guides
```

---

## Configuration

### config.json

```json
{
  "sync": {
    "detectSourceChanges": true,
    "gitComparisonStrategy": "head-vs-working",
    "gitCommitCount": 2
  }
}
```

### VS Code Settings

```json
{
  "mcpTranslation.sync.detectSourceChanges": true,
  "mcpTranslation.sync.gitComparisonStrategy": "head-vs-working",
  "mcpTranslation.sync.gitCommitCount": 2
}
```

### Environment Variables

```bash
# Enable git detection
DETECT_SOURCE_CHANGES=true

# Set strategy
GIT_COMPARISON_STRATEGY=last-n-commits

# Number of commits (for last-n-commits)
GIT_COMMIT_COUNT=5
```

---

## Use Cases

### Daily Development
```typescript
// Default: Catch all uncommitted changes
strategy: 'head-vs-working'
```

### Pre-Commit Hook
```typescript
// Only sync what's about to be committed
strategy: 'head-vs-staged'
```

### Feature Branch Review
```typescript
// Check changes in current sprint (last 10 commits)
strategy: 'last-n-commits',
commitCount: 10
```

### Release Preparation
```typescript
// Compare main branch to release branch
strategy: 'commit-range',
fromCommit: 'origin/main',
toCommit: 'release/v2.0'
```

### CI/CD Pipeline
```typescript
// Check last commit only (for incremental builds)
strategy: 'last-commit-vs-working'
```

---

## How It Works

### Detection Algorithm

1. **Fetch base content** (from git commit/ref)
2. **Fetch compare content** (from git or filesystem)
3. **Parse both as JSON**
4. **Flatten nested objects** (using dot notation: `auth.login`)
5. **Compare values**:
   - If key exists in both but values differ → **Value changed** ✅
   - If key exists in compare but not base → **New key** ✅
   - If key exists in base but not compare → **Removed key** ✅
6. **Return set of changed keys**

### Example Comparison

```typescript
// Base (HEAD):
{
  "greeting": "Hello",
  "farewell": "Goodbye"
}

// Compare (working tree):
{
  "greeting": "Hi there",  // Value changed
  "welcome": "Welcome!"    // New key
  // "farewell" removed
}

// Result:
Set(['greeting', 'welcome', 'farewell'])
```

---

## Testing

Run comprehensive tests:

```bash
npm run build
npx ts-node test/test-git-strategies.ts
```

**Tests cover:**
- ✅ Value-only changes (key stays, value changes)
- ✅ Key additions and removals
- ✅ Nested structure changes
- ✅ Staged vs unstaged detection
- ✅ Last N commits
- ✅ Commit ranges
- ✅ Placeholder preservation in changed values

---

## Migration Guide

### From Old Behavior

**Before:**
```typescript
// Only detected changes in HEAD vs working
const changed = await getChangedSourceKeysFromCommit(root, sourceLanguagePath);
```

**After:**
```typescript
// Still works (backward compatible)
const changed = await getChangedSourceKeysFromCommit(root, sourceLanguagePath);

// Or use new flexible API
const changed = await getChangedSourceKeys(root, sourceLanguagePath, {
  strategy: 'last-n-commits',
  commitCount: 5
});
```

---

## FAQs

**Q: Does it catch value-only changes?**  
**A:** YES! If the source language text changes from "Hello" to "Hi", it will detect the `greeting` key as changed.

**Q: What about placeholders?**  
**A:** It detects value changes regardless of placeholders. `"Hello, {name}"` → `"Hi, {name}"` is detected.

**Q: Can I compare two feature branches?**  
**A:** Yes! Use `commit-range` strategy:
```typescript
{
  strategy: 'commit-range',
  fromCommit: 'feature/login',
  toCommit: 'feature/signup'
}
```

**Q: What if git isn't available?**  
**A:** Falls back to TODO-based detection (existing behavior).

**Q: Performance impact?**  
**A:** Minimal. Git operations are fast, and we only parse JSON twice per sync.

---

## Implementation Details

**File:** `src/util/gitdiff.ts`

**Key Functions:**
- `getChangedSourceKeys(root, path, options)` - Main API
- `getFileAtCommit(root, path, ref)` - Helper to fetch git content
- `getChangedSourceKeysFromCommit(root, path)` - Legacy (calls new API)

**Dependencies:**
- `git show` - Fetch file content at specific ref
- `flatten()` - Convert nested JSON to dot notation
- `logger` - Debug and info logging

---

## Best Practices

1. **Use `head-vs-working` for development** - Catches everything
2. **Use `head-vs-staged` in pre-commit hooks** - Preview changes
3. **Use `last-n-commits` for sprint reviews** - Catch accumulated changes
4. **Use `commit-range` for releases** - Compare versions
5. **Always test with `dryRun: true` first** - Preview before syncing

---

## Summary

✅ **Detects value-only changes** (same key, different value)  
✅ **Detects new and removed keys**  
✅ **Supports 6 comparison strategies**  
✅ **Backward compatible** (old code still works)  
✅ **Fully tested** (9 test scenarios, all passing)  
✅ **Configurable** (config.json, VS Code settings, env vars)  

The new git strategy system gives you complete control over **when** and **how** translation re-syncing is triggered, matching your exact development workflow.
