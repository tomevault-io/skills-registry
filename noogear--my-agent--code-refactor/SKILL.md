---
name: code-refactor
description: description: "Multi-language code refactoring expert. Generates editable plan file for user approval. Trigger: refactor, clean code, organize, extract utility, DRY, naming convention, rename class." Use when this capability is needed.
metadata:
  author: noogear
---
---
name: code-refactor
description: "Multi-language code refactoring expert. Generates editable plan file for user approval. Trigger: refactor, clean code, organize, extract utility, DRY, naming convention, rename class."
---

# Code Refactoring Skill

## Phase 0: Context Discovery

### 0.1 Detect & Load Resources

```
Language Detection:
  *.java/pom.xml/build.gradle → Java
  *.py/pyproject.toml → Python  
  *.js/*.ts/package.json → JS/TS
  *.go/go.mod → Go | *.rs/Cargo.toml → Rust

Load Config: checkstyle.xml, .eslintrc.*, pyproject.toml, .golangci.yml
Load References: references/{{LANG}}/*.md, references/bundle-optimization.md
Load Related Skills: paper-plugin-dev (if Minecraft plugin detected)
```

---

## Phase 1: Plan File Workflow

**MANDATORY**: Create `.refactor-plan.md` for user review.

### Workflow Steps

| Step | Action |
|------|--------|
| 1 | Analyze scope, identify issues |
| 2 | Create `.refactor-plan.md` using template from `references/plan-template.md` |
| 3 | **Impact Assessment** — Evaluate each item (see below) |
| 4 | Notify user: "Review plan, reply Proceed/继续 or Cancel/取消" |
| 5 | **WAIT** — Do NOT proceed until user confirms |
| 6 | Parse: `[x]`=execute, `[ ]`=skip |
| 7 | Execute checked items in priority order |
| 8 | Delete plan file on success |

### Impact Assessment (Step 3)

**Before presenting plan to user, evaluate each change:**

| Check | Question | If YES |
|-------|----------|--------|
| **API Breaking** | Does this change public method signatures? | ⚠️ Mark HIGH RISK, warn user |
| **Behavior Change** | Could this alter runtime behavior? | ⚠️ Add warning in plan |
| **Test Coverage** | Are affected areas covered by tests? | If NO → Flag for manual review |
| **Dependency Chain** | Do other classes depend on this? | List affected classes |
| **Semantic Meaning** | Does rename preserve original intent? | If unclear → Ask user |

**Add assessment notes to plan file:**

```markdown
## X. Some Refactoring Item
- [x] **X.1** Rename `OldClass` → `NewClass`
  > ⚠️ **Impact**: 5 files import this class
  > ✅ **Safe**: No public API change, internal only
  > 📋 **Tests**: Covered by `OldClassTest.java`
```

**Auto-uncheck risky items:**
- Changes to public API without deprecation path → `[ ]` (unchecked by default)
- Renames that change semantic meaning → `[ ]` + warning
- Changes to classes with 0 test coverage → `[ ]` + flag

### Plan Categories (Risk Order)

1. **Quick Wins ✨** — unused imports, dead code, magic numbers
2. **Code Smells 🔧** — long methods, duplication, parameters
3. **Structure 🏗️** — method grouping, class extraction
4. **Bundle Size 📦➖** — dead code, class merge, abstraction reduction
5. **Class Responsibility 🎯** — SRP violations, misplaced classes
6. **Package Reorganization 📦** — file moves, directory creation
7. **Documentation 📝** — verbose docs, missing docs

See `references/plan-template.md` for full EN/CN templates.

---

## Phase 1.5: Naming Convention Unification

**Detect and fix inconsistent naming patterns.**

See `references/naming-conventions.md` for full rules.

### Detection

| Pattern Conflict | Example | Resolution |
|------------------|---------|------------|
| `*Manager` vs `*Service` | `ConfigManager` + `UserService` | Pick one, rename all |
| `*Utils` vs `*Util` | `StringUtils` + `FileUtil` | Unify to `*Utils` |
| `*Helper` vs `*Utils` | `ValidationHelper` + `StringUtils` | Pick one |
| `*DTO` vs `*VO` | `UserDTO` + `OrderVO` | Pick one |

### Rename Workflow

```
1. Scan classes → Group by suffix pattern
2. Identify minority pattern → Target for rename
3. Generate rename map: OldName → NewName
4. Add to plan file for user approval
5. Execute:
   a. Rename class declaration
   b. Rename file (mv command)
   c. Batch update imports
   d. Update all references
6. Validate build
```

### Plan File Entry

```markdown
## X. Naming Convention Fixes 🏷️
- [x] **X.1** Rename `FileUtil` → `FileUtils` (consistency)
- [x] **X.2** Rename `ConfigBackupManager` → `ConfigBackup` (simplify)
- [x] **X.3** Update 12 files with import changes
```

---

## Phase 2: Execution Priority

```
Low Risk:     Documentation → Constants → Rename (naming)
Medium Risk:  Extract methods → Move methods → Extract classes  
High Risk:    Bundle optimize → Class responsibility → Package restructure
```

---

## Phase 2.5: Class Responsibility (SRP)

**Execute AFTER other refactoring, BEFORE validation.**

| Pattern | Expected Location |
|---------|-------------------|
| `*Utils`, `*Helper` | `util/` |
| `*Service` | `service/` |
| `*Repository`, `*Dao` | `repository/` |
| `*Controller`, `*Handler` | `controller/` |
| `*Config` | `config/` |
| `*Exception` | `exception/` |
| `*DTO`, `*VO`, `*Entity` | `model/` |

**Key Rule**: Batch all import updates in ONE multi_replace operation.

See `references/class-responsibility.md` for full pattern table and decision flow.

---

## Phase 3: Validation (Token-Optimized)

### Batch Strategy

```
Low-Risk Items  ────→ Build only (1 checkpoint)
Medium-Risk Items ──→ Build + syntax (1 checkpoint)  
High-Risk Items ────→ Full test suite (FINAL)
```

### Skip Tests For (Safe Operations)

- Remove unused imports/variables
- Simplify comments
- Rename private methods
- Reorder methods in same class

### Must Validate (Risky Operations)

- Change method signatures
- Move methods between classes
- Extract to new class/file
- Change package structure
- Modify public API

### Deferred Testing

```
1. Make ALL code changes
2. IDE error check (instant, free)
3. Build ONCE at end
4. Test ONCE (if build passes)
```

### Error Recovery

If final validation fails:
1. **DO NOT revert all changes** — use git diff to identify problem
2. Fix only the broken part
3. Re-validate only affected scope

---

## Phase 4: Bundle Size Optimization

See `references/bundle-optimization.md` for detailed tables.

### Key Actions

| Category | Action |
|----------|--------|
| Dead Code | Delete unused classes/methods/fields/deps |
| Class Merge | Combine tiny utils, inline single-use helpers |
| Abstractions | Remove interface+1 impl, merge abstract+child |
| Directories | Consolidate single-class packages, delete empty dirs |

### Directory Cleanup Rules

| Package Type | Min Classes | Action if Below |
|--------------|-------------|-----------------|
| `util/` | 3 | Merge with parent |
| `exception/` | 2 | Keep |
| `api/` | 1 | Keep (stability) |
| Other | 2 | Consider merging |

### Empty Directory Cleanup

```powershell
# After file moves, remove empty dirs
Get-ChildItem -Directory -Recurse | Where-Object { (Get-ChildItem $_.FullName).Count -eq 0 } | Remove-Item
```

---

## Safety Checklist

| Phase | Check |
|-------|-------|
| Before | Git clean, feature branch |
| During | Batch changes, check IDE errors, NO intermediate tests |
| After | Build once, test once, commit together |

---

## Incremental Strategy (Large Codebases)

```
Session 1: Quick wins
Session 2: Documentation
Session 3: Extract methods
Session 4: Extract classes
Session 5: Package restructure
```

Each session: Build → Test → Commit


---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noogear) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
