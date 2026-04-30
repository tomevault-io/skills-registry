---
name: release-skills
description: | Use when this capability is needed.
metadata:
  author: openclaw
---

# Release Skills

Universal release workflow supporting any project type with multi-language changelog generation.

## Supported Projects

| Type | Version File | Auto-Detected |
|------|--------------|---------------|
| Node.js | package.json | ✓ |
| Python | pyproject.toml | ✓ |
| Rust | Cargo.toml | ✓ |
| Claude Plugin | marketplace.json | ✓ |
| Generic | VERSION / version.txt | ✓ |

## Options

| Flag | Description |
|------|-------------|
| `--dry-run` | Preview changes without executing |
| `--major` | Force major version bump |
| `--minor` | Force minor version bump |
| `--patch` | Force patch version bump |

---

## Workflow

### Step 1: Detect Configuration

1. Check for `.releaserc.yml` (optional config)
2. Auto-detect version file (priority: package.json → pyproject.toml → Cargo.toml → marketplace.json → VERSION)
3. Scan for changelog files: `CHANGELOG*.md`, `HISTORY*.md`, `CHANGES*.md`
4. Identify language of each changelog by suffix

**Language Detection**:
| Pattern | Language |
|---------|----------|
| `CHANGELOG.md` (no suffix) | en |
| `CHANGELOG.zh.md` / `CHANGELOG_CN.md` | zh |
| `CHANGELOG.ja.md` / `CHANGELOG_JP.md` | ja |
| `CHANGELOG.{lang}.md` | Corresponding language |

Output:
```
Project detected:
  Version file: package.json (1.2.3)
  Changelogs: CHANGELOG.md (en), CHANGELOG.zh.md (zh)
```

### Step 2: Analyze Changes

```bash
LAST_TAG=$(git tag --sort=-v:refname | head -1)
git log ${LAST_TAG}..HEAD --oneline
```

Categorize by conventional commit:
- `feat:` → Features
- `fix:` → Fixes  
- `docs:` → Documentation
- `refactor:` → Refactor
- `perf:` → Performance
- `chore:` → Skip in changelog

**Breaking Change Detection**:
- `BREAKING CHANGE` in message or body
- Removed public APIs, renamed exports

Warn if breaking changes: "Consider major version bump (--major)."

### Step 3: Determine Version

Priority:
1. User flag (`--major/--minor/--patch`)
2. BREAKING CHANGE → Major (1.x.x → 2.0.0)
3. `feat:` present → Minor (1.2.x → 1.3.0)
4. Otherwise → Patch (1.2.3 → 1.2.4)

Display: `1.2.3 → 1.3.0`

### Step 4: Generate Changelogs

For each changelog file:

1. Identify language from filename
2. Detect third-party contributors via merged PRs
3. Generate content in that language:
   - Section titles in target language
   - Date format: YYYY-MM-DD
   - Attribution: `(by @username)` for non-owner contributors
4. Insert at file head, preserve existing content

**Section Titles**:
| Type | en | zh | ja |
|------|----|----|-----|
| feat | Features | 新功能 | 新機能 |
| fix | Fixes | 修复 | 修正 |
| docs | Documentation | 文档 | ドキュメント |
| breaking | Breaking Changes | 破坏性变更 | 破壊的変更 |

**Format**:
```markdown
## 1.3.0 - 2026-01-22

### Features
- Add user authentication (by @contributor1)
- Support OAuth2 login

### Fixes
- Fix memory leak in connection pool
```

### Step 5: Group by Module (Optional)

For monorepos, group commits by affected skill/module:

```
baoyu-cover-image:
  - feat: add new style options
  → README updates: options table

baoyu-comic:
  - refactor: improve panel layout
  → No README updates
```

### Step 6: User Confirmation

Present:
- Changelog preview
- Proposed version bump
- Changes summary

Ask:
1. Confirm version bump (show recommended)
2. Push to remote? (Yes/No)

### Step 7: Create Release

```bash
# Stage files
git add <version-file> CHANGELOG*.md

# Commit
git commit -m "chore: release v{VERSION}"

# Tag
git tag v{VERSION}

# Push (if confirmed)
git push origin main
git push origin v{VERSION}
```

**Output**:
```
Release v1.3.0 created.
Tag: v1.3.0
Status: Pushed to origin
```

---

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/prepare_release.py` | Prepare release with version bump |
| `scripts/release_notes.py` | Generate release notes from commits |
| `scripts/roadmap_changelog.py` | Generate changelog from roadmap |

---

## Configuration (.releaserc.yml)

Optional overrides:

```yaml
version:
  file: package.json
  path: $.version

changelog:
  files:
    - path: CHANGELOG.md
      lang: en
    - path: CHANGELOG.zh.md
      lang: zh

commit:
  message: "chore: release v{version}"

tag:
  prefix: v
```

---

## Dry-Run Mode

With `--dry-run`:
- Show all proposed changes
- Preview changelog entries
- List commits to create
- No actual changes made

---

## Version Paths

| File | Path |
|------|------|
| package.json | `$.version` |
| pyproject.toml | `project.version` |
| Cargo.toml | `package.version` |
| marketplace.json | `$.metadata.version` |
| VERSION | Direct content |

---

## Quality Criteria

Good releases:
- Clear changelog entries describing user-facing changes
- Proper contributor attribution
- Consistent multi-language content
- No orphaned tags (always with commit)
- Version bump matches change significance

---

## NEVER

- Force push to main/master
- Skip user confirmation before push
- Create tags without commits
- Include internal/chore changes in user-facing changelog
- Push without explicit user consent
- Add Co-Authored-By to release commits (they're automated)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
