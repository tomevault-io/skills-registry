---
name: auto-doc
description: Automatic codebase documentation maintenance system. Use this skill ALWAYS when making ANY code changes including creating, modifying, deleting, or moving files. Enforces three-tier documentation (root ARCHITECTURE.md, folder INDEX.md, file header comments) that must be updated after every code change to keep documentation synchronized with codebase. Use when this capability is needed.
metadata:
  author: cl-victor1
---

# Auto Doc

Automatic documentation maintenance system with three-tier structure. **MUST be applied after every code change.**

## Three-Tier Documentation Structure

### Tier 1: Root ARCHITECTURE.md

Location: Project root directory.

Content requirements:
- High-level architecture overview (max 10 lines)
- Links to all subdirectory INDEX.md files
- **Update trigger**: Any feature, architecture, or structural change

Header declaration:
```markdown
<!-- AUTO-DOC: Update me when project structure or architecture changes -->
```

### Tier 2: Folder INDEX.md

Location: Every folder containing code files.

Format (max 3 lines for overview):
```markdown
<!-- AUTO-DOC: Update me when files in this folder change -->

# [FolderName]

[1-3 line architecture description]

## Files

| File | Role | Function |
|------|------|----------|
| example.ts | Core | Main entry point |
```

### Tier 3: File Header Comments

Location: Top of every code file (first 3-5 lines after imports).

Format:
```typescript
/**
 * @input Dependencies this file requires from external sources
 * @output What this file provides/exports to other parts of the system
 * @position Role and importance in the local architecture
 * @auto-doc Update header and folder INDEX.md when this file changes
 */
```

Language-specific formats:

**TypeScript/JavaScript:**
```typescript
/**
 * @input { UserService } from './services', { config } from '@/config'
 * @output { AuthProvider, useAuth } React context and hook for authentication
 * @position Core auth layer, wraps entire app
 * @auto-doc Update header and folder INDEX.md when this file changes
 */
```

**Python:**
```python
"""
@input: requests, json from stdlib; Config from ./config
@output: APIClient class for external service calls
@position: Network layer abstraction
@auto-doc: Update header and folder INDEX.md when this file changes
"""
```

**Go:**
```go
// @input: net/http, encoding/json; config from ./internal/config
// @output: Handler struct, NewHandler(), ServeHTTP()
// @position: HTTP request handler for /api/users
// @auto-doc: Update header and folder INDEX.md when this file changes
```

## Mandatory Update Workflow

After ANY code change, execute in order:

1. **Update file header** - Reflect new input/output/position if changed
2. **Update folder INDEX.md** - Add/remove/modify file entry
3. **Update root ARCHITECTURE.md** - Only if structural change

## Quick Reference

| Change Type | Update Header | Update INDEX.md | Update ARCHITECTURE.md |
|-------------|---------------|-----------------|------------------------|
| Edit file logic | If I/O changes | If role changes | No |
| Create file | Yes (new) | Yes (add entry) | If new feature |
| Delete file | N/A | Yes (remove) | If structural |
| Move file | Yes (new pos) | Both folders | If structural |
| Rename file | Yes | Yes | If structural |

## Example: Creating New File

When creating `lib/utils/format.ts`:

1. Add header to new file:
```typescript
/**
 * @input { date-fns } for date formatting
 * @output { formatDate, formatCurrency } utility functions
 * @position Shared formatting utilities
 * @auto-doc Update header and folder INDEX.md when this file changes
 */
```

2. Update or create `lib/utils/INDEX.md`:
```markdown
<!-- AUTO-DOC: Update me when files in this folder change -->

# Utils

Shared utility functions for formatting, validation, and helpers.

## Files

| File | Role | Function |
|------|------|----------|
| format.ts | Utility | Date and currency formatting |
```

3. Update `ARCHITECTURE.md` if `lib/utils/` is new.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cl-victor1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
