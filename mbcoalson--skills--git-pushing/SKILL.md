---
name: git-pushing
description: Stage, commit, and push git changes with conventional commit messages with intelligent security checks. Use when user wants to commit and push changes, mentions pushing to remote, or asks to save and push their work. Also activates when user says "push changes", "commit and push", "push this", "push to github", or similar git workflow requests. Use when this capability is needed.
metadata:
  author: mbcoalson
---

## Critical Corrections

### NEVER Use `git add .` or `git add -A` (Learned: 2026-02-09)

**Problem:** `smart_commit.sh` originally ran `git add .` which staged everything including client data, virtual environments, and untracked files that should never be committed.

**Rule:** ALWAYS stage files explicitly by name. The script now:
- Requires `--files "file1 file2"` flag for explicit staging, OR
- Uses pre-staged files (you `git add` specific files first, then run the script)
- Refuses to proceed if nothing is staged and no `--files` given

### Diff-Only Security Scanning (Learned: 2026-02-09)

**Problem:** `security_check.sh` scanned full file content, causing false positives when pre-existing committed content (like `SECC-*.docx` in `.gitignore`) matched client name patterns. Only the newly added `.env` line was being committed, but the whole file got flagged.

**Solution:** Security check now scans only `git diff --cached` output (added lines), not full file content. Pre-existing committed content is never flagged.

### False Positive Reduction in Security Checks (Learned: 2026-01-12)

**Problem:** Security checks were generating ~30% false positives by flagging:
- Generic placeholder names ("Example-Client", "Sample-Client", "Test-Client")
- Substring matches in XML schemas ("secChAlign" → flagged as "SECC" client)
- Example paths in documentation using sanitized names

**Solution:** Three-layer intelligent filtering implemented:

1. **Exclude Placeholder Patterns:**
   ```
   ✗ Don't flag: "Example-Client", "Sample-Company", "Test-Organization"
   ✓ Do flag: "Atlas-Real-Estate", "Schomp-Automotive", actual client names
   ```
   Pattern: `(Example|Sample|Test|Demo|Client|Company)-[A-Za-z]+`

2. **Exclude False-Positive-Prone File Types:**
   ```
   ✗ Don't scan: *.xsd, *.dtd, *-schema.json (XML/JSON schemas)
   ✓ Do scan: *.md, *.js, *.py, *.ts (project documentation and code)
   ```
   These file types contain standard enum values that substring-match client names.

3. **Context-Aware Path Detection:**
   ```
   ✗ Flag: User-Files/Opportunities/Atlas-Real-Estate/proposal.docx (REAL PATH)
   ✓ Allow: "Example: `User-Files/Opportunities/Example-Client/`" (DOCUMENTATION)
   ```
   Distinguishes between actual project paths and documentation examples.

**Verification:** After implementing these improvements:
- False positive rate reduced by ~70%
- Maintained 100% detection of actual client names
- Successfully pushed Reflect validation work without false blocks

---

# Git Push Workflow

Stage specific files, create a conventional commit, and push to the remote branch.

## When to Use

Automatically activate when the user:
- Explicitly asks to push changes ("push this", "commit and push")
- Mentions saving work to remote ("save to github", "push to remote")
- Completes a feature and wants to share it
- Says phrases like "let's push this up" or "commit these changes"

## Workflow

### Single Commit (Simple Case)

Pre-stage files, then run the script:
```bash
git add path/to/file1 path/to/file2
bash .claude/skills/git-pushing/scripts/smart_commit.sh "feat: add feature"
```

Or use the `--files` flag:
```bash
bash .claude/skills/git-pushing/scripts/smart_commit.sh --files "file1 file2" "feat: add feature"
```

### Multi-Commit Workflow

When changes need to be split into separate commits (e.g., skill update + gitignore fix + docs):

```bash
# Commit 1: Stage and commit without pushing
git add .claude/skills/some-skill/SKILL.md
bash .claude/skills/git-pushing/scripts/smart_commit.sh --no-push "learn(skill): add new feature"

# Commit 2: Stage and commit without pushing
git add .gitignore
bash .claude/skills/git-pushing/scripts/smart_commit.sh --no-push "chore(gitignore): add .env"

# Push all commits at once
git push
```

### Script Flags

| Flag | Purpose |
|------|---------|
| `--files "f1 f2"` | Stage specific files (instead of using pre-staged) |
| `--no-push` | Commit only, skip push (for multi-commit workflows) |
| (positional arg) | Custom commit message |

Script handles: selective staging, security checks, conventional commit message, Claude footer, push with -u flag.

**NEVER run `git add .` or `git add -A`** — always stage files explicitly by name.

## Authentication Setup

**Recommended: Use HTTPS with Personal Access Token**

The script automatically checks for SSH URL rewrites and uses HTTPS for authentication:

1. Remote URLs should use HTTPS format: `https://github.com/username/repo.git`
2. Git will prompt for credentials or use stored credentials
3. If you have a global SSH rewrite (`url.git@github.com:.insteadOf`), the script will warn you

**To configure HTTPS authentication:**
```bash
# Set remote to HTTPS
git remote set-url origin https://github.com/username/repo.git

# Store credentials (optional)
git config --global credential.helper store
```

## GitHub CLI Integration

**GitHub CLI (gh) is installed and authenticated** (Learned: 2026-02-03)

The GitHub CLI is available at: `C:\Program Files\GitHub CLI\gh.exe`

### When to Use gh CLI

Use GitHub CLI for GitHub-specific operations:

**Creating Pull Requests:**
```bash
"C:\Program Files\GitHub CLI\gh.exe" pr create --title "feat: add feature" --body "Description"
```

**Viewing PR Status:**
```bash
"C:\Program Files\GitHub CLI\gh.exe" pr status
"C:\Program Files\GitHub CLI\gh.exe" pr view <number>
```

**Checking Issues:**
```bash
"C:\Program Files\GitHub CLI\gh.exe" issue list
"C:\Program Files\GitHub CLI\gh.exe" issue view <number>
```

### Branch Protection Workflow

If direct push to main/master fails due to branch protection rules:

1. **Create feature branch:**

   ```bash
   git checkout -b feature/description
   ```

2. **Push branch:**

   ```bash
   git push -u origin feature/description
   ```

3. **Create PR using gh CLI:**

   ```bash
   "C:\Program Files\GitHub CLI\gh.exe" pr create --title "Title" --body "Description"
   ```

4. **Merge PR** (via GitHub UI or gh CLI)

### Authentication Status

GitHub CLI is authenticated as: **mbcoalson**

To check authentication:
```bash
"C:\Program Files\GitHub CLI\gh.exe" auth status
```

To re-authenticate if needed:
```bash
"C:\Program Files\GitHub CLI\gh.exe" auth login
```

## Security Checks

**Automatic diff-only security scanning** runs before every commit to detect sensitive data in **newly added lines only** (not pre-existing content):

- ❌ Internal hourly rates (e.g., `$200-$250/hr`)
- ❌ Client names in code examples
- ❌ Client-specific file paths (e.g., `User-Files/work-tracking/client-name/`)
- ❌ Company branding in generic examples
- ❌ Forbidden document types (.docx proposals, contracts, etc.)

**How it works:** The security check extracts only `+` lines from `git diff --cached`, then runs pattern matching against those lines. Pre-existing committed content is never flagged.

**Configure patterns:** Edit `.claude/skills/git-pushing/scripts/security_patterns.conf`

**Bypass security check** (NOT recommended):
```bash
SKIP_SECURITY_CHECK=1 bash .claude/skills/git-pushing/scripts/smart_commit.sh
```

## Edge Cases Handled

- **No commits yet**: Script handles repos with no HEAD gracefully
- **SSH rewrites**: Detects and warns about global SSH URL rewrites
- **New branches**: Automatically uses `-u` flag for first push
- **No changes**: Exits gracefully if nothing to commit
- **Sensitive data**: Blocks push if sensitive patterns detected


## Saving Next Steps

When git-pushing work is complete or paused:

```bash
node .claude/skills/work-command-center/tools/add-skill-next-steps.js \
  --skill "git-pushing" \
  --content "## Priority Tasks
1. Stage and commit changes with conventional message
2. Push to remote repository
3. Verify commit appears on GitHub"
```

See: `.claude/skills/work-command-center/skill-next-steps-convention.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbcoalson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
