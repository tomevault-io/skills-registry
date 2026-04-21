---
name: 17th-doc-checker
description: | Use when this capability is needed.
metadata:
  author: seventeenthearth
---

# Doc Checker

Identify and apply documentation updates after implementation work.

## When to Use

- After completing TASK implementation
- Before creating a PR
- When code changes affect documented behavior

## What It Checks

| Document | Check For |
|----------|-----------|
| `docs/docs/dev/architecture.md` | New patterns, changed flows |
| `docs/docs/dev/server-implementation.md` | Implementation details |
| `docs/docs/dev/server-roadmap.md` | TASK completion status |
| `docs/docs/dev/client-roadmap.md` | Related client TASK updates |
| `docs/docs/dev/architecture-decision-records.md` | New ADRs needed |
| `docs/contracts/data.json` | API contract changes |
| `docs/proto/**` | Proto documentation comments |
| `CLAUDE.md` | Project guidance updates |

## Usage

```
/doc-checker                  → Analyze current branch changes
/doc-checker --staged         → Only staged changes
```

## Workflow

Claude performs all steps directly (no subagent/GLM delegation).

### Step 1: Gather Context

```bash
# Get current branch
git branch --show-current

# Get changed files vs main
git diff main...HEAD --name-only

# Get diff summary
git diff main...HEAD --stat
```

### Step 2: Read TASK.md

Read TASK.md to understand:
- What was implemented
- Key decisions made
- Scope and boundaries

### Step 3: Read Relevant Docs

Based on changed files, read relevant documentation:

| If Changed | Read |
|------------|------|
| `docs/proto/**` | `docs/contracts/data.json`, `server-implementation.md` |
| `internal/feature/space/**` | `architecture.md` (Space section) |
| `internal/infrastructure/middleware/**` | `architecture.md`, `architecture-decision-records.md` |
| Any feature code | `server-roadmap.md` (TASK status) |

### Step 4: Analyze & Compare

For each document, check:

1. **Outdated Information**
   - Statements no longer accurate
   - Examples needing updates
   - Diagrams needing revision

2. **Missing Information**
   - New features not documented
   - Changed behavior not reflected
   - New patterns introduced

3. **Roadmap Updates**
   - TASK status (planned → completed)
   - Dependencies discovered
   - Timeline changes

4. **Contract Updates**
   - API changes in data.json
   - Proto field changes
   - Request/response format changes

### Step 5: Present Findings

```markdown
# Documentation Sync Report

## Summary
- X documents need updates

## Updates Needed

### 1. {document_path}

**Section**: {section_name}

**Current**:
> {current_text}

**Update to**:
> {updated_text}

**Reason**: {why}

---

## No Updates Needed
- {document}: {reason_still_accurate}
```

### Step 6: Apply Updates

After user confirms:
- Use Edit tool to apply each change
- Verify changes are correct

## Documents Priority

### Always Check

```
docs/docs/dev/server-roadmap.md      # TASK status
docs/docs/dev/architecture.md        # Architectural changes
docs/contracts/data.json             # API contracts
```

### Check If Proto Changed

```
docs/proto/**/*.proto                # Proto comments
docs/docs/dev/server-implementation.md
```

### Check If Auth/Middleware Changed

```
docs/docs/dev/architecture-decision-records.md
CLAUDE.md
```

## Example

```
User: /doc-checker

Claude:
Checking branch task-177 changes...

Changed files:
- docs/proto/space/v1/space.proto
- internal/feature/space/protocol/manager.go
- internal/feature/space/application/stream/service.go
+ 42 more files

Reading TASK.md...
Reading docs/docs/dev/server-roadmap.md...
Reading docs/contracts/data.json...
Reading docs/docs/dev/architecture.md...

# Documentation Sync Report

## Summary
- 3 documents need updates

## Updates Needed

### 1. docs/docs/dev/server-roadmap.md

**Section**: TASK-177 entry

**Current**:
> - TASK-177: Profile Identity 최종 전환 — planned

**Update to**:
> - TASK-177: Profile Identity 최종 전환 — **completed**

**Reason**: Implementation finished

---

### 2. docs/contracts/data.json

**Section**: StreamSpace patch examples

**Current**:
> "path": "/participants/{user_id}"

**Update to**:
> "path": "/participants/{profile_id}"

**Reason**: Patch paths now use profile_id

---

Apply these updates? (y/n)

User: y

Claude:
Updating docs/docs/dev/server-roadmap.md...
Updating docs/contracts/data.json...

Done. 2 documents updated.
```

## Notes

- Claude performs analysis directly (efficient, no delegation overhead)
- Reads files once, then applies updates
- User confirms before any changes
- Focuses on documents affected by actual code changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seventeenthearth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
