---
name: jj-pr
description: Create GitHub PR from jj workspace (works without .git) Use when this capability is needed.
metadata:
  author: litl-l
---

# Create PR from jj Workspace

## Steps

1. **Verify changes exist**
   ```bash
   jj status
   jj log -r @
   ```

2. **Create conventional bookmark** (if not exists)
   ```bash
   # Format: <type>/<short-name>
   jj bookmark create feature/my-feature -r @
   ```

3. **Push with named bookmark**
   ```bash
   jj git push --bookmark <type>/<name>
   ```

4. **Get repo info**
   ```bash
   REMOTE=$(jj git remote list | grep origin | awk '{print $2}')
   OWNER_REPO=$(echo "$REMOTE" | sed 's|.*github.com[:/]||' | sed 's|\.git$||')
   ```

5. **Create PR via API**
   ```bash
   gh api repos/$OWNER_REPO/pulls \
     -f title="$TITLE" \
     -f head="<type>/<name>" \
     -f base="main" \
     -f body="$BODY"
   ```

## Bookmark Naming

**Format**: `<type>/<short-name>`

Examples:
- `feature/jj-rules`
- `fix/auth-timeout`
- `refactor/db-queries`
- `docs/readme-update`

## PR Title Format

Follow gitmoji + conventional commit with **mandatory scope**:
- `:sparkles: feat(scope): description`
- `:bug: fix(scope): description`
- `:recycle: refactor(scope): description`

## Arguments

$ARGUMENTS

If empty, generate title from `jj log -r @ -T description`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/litl-l) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
