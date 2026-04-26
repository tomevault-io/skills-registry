---
name: todo-finder
description: Finds and organizes TODO, FIXME, HACK, and NOTE comments in codebase. Use when user asks to "find TODOs", "show all FIXMEs", "list action items", or "what needs to be done". Use when this capability is needed.
metadata:
  author: dexploarer
---

# TODO Finder Skill

Searches codebase for TODO, FIXME, HACK, NOTE, and similar comments, organizing them by priority and file.

## When to Use

This skill activates when the user wants to find action items in code:
- "Find all TODOs in the project"
- "Show me what FIXMEs we have"
- "List all the HACKs in the codebase"
- "What needs to be done in this project?"
- "Find all code comments with action items"

## Instructions

### Step 1: Search for Action Item Comments

Use Grep to find common action item markers:
- **TODO**: Things that need to be done
- **FIXME**: Things that need to be fixed
- **HACK**: Temporary solutions that need improvement
- **BUG**: Known bugs
- **NOTE**: Important notes
- **XXX**: Warnings or important items
- **OPTIMIZE**: Performance improvements needed
- **REFACTOR**: Code that needs refactoring

### Step 2: Organize by Priority

Group findings by severity:
1. **Critical** (FIXME, BUG)
2. **Important** (TODO, HACK)
3. **Informational** (NOTE, OPTIMIZE, REFACTOR)

### Step 3: Format the Output

Display results in a clear, actionable format:

```
📋 Code Action Items Summary
================================

🔴 Critical (2 items):

📁 src/auth/login.js:45
   FIXME: Authentication bypass vulnerability if user is null

📁 src/api/payments.js:127
   BUG: Payment processing fails for amounts over $10,000

⚠️  Important (5 items):

📁 src/components/Header.js:23
   TODO: Add mobile responsive menu

📁 src/utils/validation.js:89
   HACK: Using regex for email validation, should use proper library

📁 src/services/api.js:156
   TODO: Implement retry logic for failed requests

📁 src/db/connection.js:34
   HACK: Hardcoded connection pool size, should be configurable

📁 src/pages/Dashboard.js:78
   TODO: Add loading skeleton for better UX

ℹ️  Informational (3 items):

📁 src/utils/formatting.js:67
   NOTE: Date format follows ISO 8601 standard

📁 src/algorithms/search.js:234
   OPTIMIZE: Can use binary search here for O(log n) instead of O(n)

📁 src/components/Table.js:145
   REFACTOR: This component is too large, split into smaller components

================================
Total: 10 action items
```

### Step 4: Provide Context

For each item, show:
- File path and line number
- The comment type (TODO, FIXME, etc.)
- The full comment text
- Surrounding context if helpful

### Step 5: Suggest Next Steps

Recommend actions based on findings:
- If many FIXME/BUGs: "Consider addressing critical issues first"
- If many HACKs: "These temporary solutions should be properly implemented"
- If many TODOs: "Prioritize and add to your backlog"

## Examples

### Example 1: Basic Search

**User:** "Find all TODOs in this project"

**Response:**
```
Searching for TODO comments...

Found 7 TODO items:

📁 src/components/Dashboard.js:45
   TODO: Add error boundary for better error handling

📁 src/hooks/useAuth.js:23
   TODO: Implement token refresh logic

📁 src/services/analytics.js:89
   TODO: Add event batching to reduce API calls

📁 src/pages/Settings.js:156
   TODO: Add form validation

📁 src/utils/cache.js:67
   TODO: Implement cache expiration

📁 tests/unit/auth.test.js:34
   TODO: Add tests for edge cases

📁 docs/API.md:12
   TODO: Document new endpoints

Total: 7 TODO items found
```

### Example 2: Multiple Types

**User:** "Show me all action items - TODOs, FIXMEs, everything"

**Response:**
```
Scanning codebase for action items...

🔴 CRITICAL (1):

📁 src/api/users.js:234
   FIXME: SQL injection vulnerability in search query

⚠️  IMPORTANT (4):

📁 src/auth/session.js:67
   TODO: Implement session timeout

📁 src/components/ImageUpload.js:45
   HACK: Using setTimeout instead of proper image processing library

📁 src/utils/validator.js:123
   TODO: Add input sanitization

📁 src/config/db.js:28
   BUG: Connection pool exhaustion under high load

ℹ️  INFORMATIONAL (2):

📁 src/algorithms/sort.js:89
   OPTIMIZE: Use quicksort instead of bubble sort

📁 src/components/LargeForm.js:234
   REFACTOR: Split into smaller components

Total: 7 items (1 critical, 4 important, 2 informational)
```

### Example 3: Specific File

**User:** "Find TODOs in the authentication module"

**Response:**
```
Searching for TODOs in authentication files...

📁 src/auth/login.js:
   Line 45: TODO: Add 2FA support
   Line 89: TODO: Implement remember me functionality
   Line 156: NOTE: Password hashing uses bcrypt with salt rounds of 12

📁 src/auth/session.js:
   Line 23: TODO: Add session cleanup cron job
   Line 67: FIXME: Session storage not working in Safari
   Line 123: TODO: Implement single sign-on

📁 src/auth/permissions.js:
   Line 34: TODO: Add role-based access control
   Line 78: HACK: Permissions hardcoded, should come from database

Total: 8 items in authentication module
```

## Search Patterns

### Standard Markers
```
TODO:     - Tasks to be completed
FIXME:    - Things that are broken and need fixing
BUG:      - Known bugs
HACK:     - Temporary workarounds
XXX:      - Important warnings
NOTE:     - Important information
OPTIMIZE: - Performance improvements needed
REFACTOR: - Code quality improvements needed
```

### Extended Markers
```
REVIEW:   - Needs code review
TEST:     - Needs testing
DOC:      - Documentation needed
CLEANUP:  - Code cleanup needed
SECURITY: - Security concerns
```

## Grep Command Examples

### Find all TODO comments:
```bash
grep -rn "TODO:" --include="*.js" --include="*.ts" --include="*.py" .
```

### Find multiple types:
```bash
grep -rn -E "(TODO|FIXME|HACK|BUG):" --include="*.js" .
```

### Case-insensitive search:
```bash
grep -rni "todo:" .
```

## Output Formatting Tips

### Group by Priority
1. Show critical items first (FIXME, BUG)
2. Then important items (TODO, HACK)
3. Finally informational items

### Show Context
- Include file path and line number
- Show the full comment
- Add surrounding code if it helps understanding

### Make It Actionable
- Count items by type
- Suggest which to tackle first
- Note patterns (e.g., many TODOs in one file)

## Tips

### For Complete Search
- Search all relevant file types (.js, .ts, .py, .java, etc.)
- Include test files
- Check documentation files
- Don't forget config files

### For Better Organization
- Group by file or by type
- Sort by priority
- Show totals and summaries
- Highlight critical items

### For Context
- Show line numbers for easy navigation
- Include surrounding code if helpful
- Note who added it if in git blame
- Show when it was added (if checking git history)

## Common Variations

### Search Specific Directory
"Find TODOs in the components folder"

### Search by Type
"Show me all FIXME comments"

### Search by Priority
"What are the critical issues in the code?"

### Search with Context
"Find TODOs and show me the code around them"

## Edge Cases

### No Results
```
✨ Great news! No TODO items found in the codebase.

Either the project is complete or TODOs are tracked elsewhere (like GitHub Issues).
```

### Too Many Results
```
⚠️  Found 127 TODO items - that's a lot!

Showing top 10 by priority. Use 'find TODOs in [specific folder]' to narrow down.
```

### Commented Out TODOs
```
Note: Found some commented-out TODOs (e.g., // // TODO)
These might be old items that were resolved. Consider removing them.
```

## Remember

- **Search broadly**: Include all file types
- **Organize clearly**: Group by priority/type
- **Make actionable**: Show file:line for easy navigation
- **Add value**: Don't just list, organize and prioritize
- **Suggest next steps**: Help user decide what to tackle first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dexploarer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
