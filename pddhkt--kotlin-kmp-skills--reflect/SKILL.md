---
name: reflect
description: description: Push improvements back to Kotlin Multiplatform Skills source repository. Use after encountering issues with KMP, Android, iOS, Ktor, Koin, or SQLDelight skills. Use when this capability is needed.
metadata:
  author: pddhkt
---
---
name: reflect
description: Push improvements back to Kotlin Multiplatform Skills source repository. Use after encountering issues with KMP, Android, iOS, Ktor, Koin, or SQLDelight skills.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - AskUserQuestion
---

# Reflect — Kotlin Multiplatform Skills

Capture learnings from the current session and push improvements back to the `kotlin-kmp-skills` source repository.

## When to Use

Run `/kotlin-kmp:reflect` when you've:
- Encountered API mismatches (wrong Kotlin APIs, deprecated patterns)
- Discovered undocumented KMP patterns
- Found workflow improvements for shared/platform modules
- Fixed common error patterns that should be documented

## Workflow

```
┌─────────────────────────────────────────────────────────────┐
│  Current Session                                            │
│                                                             │
│  1. Analyze conversation for improvements                   │
│  2. Pre-check: Compare installed vs latest version          │
│     - If outdated → prompt to update first                  │
│     - If current → continue                                 │
│  3. Locate kotlin-kmp-skills repo                           │
│  4. Show proposed changes for approval                      │
│  5. Apply changes to skill files                            │
│  6. Commit and push to origin                               │
│  7. Update plugin in current project                        │
│     - Run: /plugin marketplace update kotlin-kmp-skills     │
│  8. Verify new version is installed                         │
└─────────────────────────────────────────────────────────────┘
```

## Process

### Step 1: Version Check

Before analyzing improvements, check if the installed plugin is up-to-date:

```bash
# Compare installed version with latest in source repo
# If outdated, prompt user to update first to avoid duplicate changes
```

If outdated:
```
The installed kotlin-kmp plugin is outdated. Please update first:

  /plugin marketplace update kotlin-kmp-skills

Then run /kotlin-kmp:reflect again.
```

### Step 2: Analyze Context

Review the current conversation for:

| Category | Look For |
|----------|----------|
| **API Mismatches** | Wrong function signatures, deprecated APIs used |
| **Missing Documentation** | Undocumented patterns discovered during work |
| **Workflow Improvements** | Better approaches found for common tasks |
| **Error Patterns** | Common mistakes that should have warnings |
| **Template Updates** | Outdated code templates that needed fixes |

### Step 3: Locate Source Repository

Find the kotlin-kmp-skills repository:

1. Check `KOTLIN_KMP_SKILLS_REPO` environment variable
2. Default to `~/Projects/personal/kotlin-kmp-skills`
3. Clone from GitHub if not present locally:
   ```bash
   git clone git@github.com:pddhkt/kotlin-kmp-skills.git
   ```

### Step 4: Propose Changes

Show the user what will be updated:

```markdown
## Proposed Skill Improvements

### kmp/reference/expect-actual.md
- Add iOS-specific actual implementation for FileSystem
- Update Gradle configuration for KMP 2.0

### sqldelight/reference/schema.md
- Fix migration syntax for nullable columns

### Reasoning
During this session, we encountered build failures because the
expect/actual skill was missing the iOS FileSystem implementation.

---
Proceed with these changes? (y/n)
```

### Step 5: Apply Changes

After user approval:

1. Navigate to source repo
2. Edit the relevant skill files under `plugins/kotlin-kmp/`
3. Create a descriptive commit:
   ```bash
   git commit -m "reflect: add iOS FileSystem actual, fix migration syntax

   - kmp/reference/expect-actual.md: Add iOS FileSystem implementation
   - sqldelight/reference/schema.md: Fix nullable column migration

   Source: Session reflection"
   ```

### Step 6: Push to Origin

```bash
git push origin main
```

### Step 7: Update Current Project

After pushing, update the plugin in the current project:

```
/plugin marketplace update kotlin-kmp-skills
```

Verify the new version is installed and the changes are reflected.

### Step 8: Notify Team

Output a summary:

```markdown
## Skills Updated Successfully

The following changes have been pushed to kotlin-kmp-skills:
- kmp/reference/expect-actual.md: Added iOS FileSystem actual
- sqldelight/reference/schema.md: Fixed migration syntax

Your local installation has been updated.

**For other team members:**
Run `/plugin marketplace update kotlin-kmp-skills` to get these improvements.
```

## Configuration

Set a custom repository location:

```bash
# In your shell profile (~/.bashrc, ~/.zshrc)
export KOTLIN_KMP_SKILLS_REPO="/custom/path/to/kotlin-kmp-skills"
```

## What Gets Captured

### Good Candidates for Reflection

- API signature corrections
- Missing configuration steps discovered
- Better Kotlin/KMP patterns found
- Common errors and their fixes
- Updated Gradle configuration
- New expect/actual implementations

### Not Captured

- Project-specific code
- Credentials or secrets
- Personal preferences (unless universally beneficial)
- Temporary workarounds

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pddhkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
