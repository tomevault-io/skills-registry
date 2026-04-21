---
name: changelog
description: Generate or update CHANGELOG.md using git-cliff from conventional commit history - see https://git-cliff.org/docs/category/usage Use when this capability is needed.
metadata:
  author: jason-hchsieh
---

# Generate Changelog

You are generating or updating a CHANGELOG.md file using git-cliff. For complete usage documentation, visit the [git-cliff usage guide](https://git-cliff.org/docs/category/usage).

## Prerequisites

Check that git-cliff is installed:

```bash
git-cliff --version
```

If not installed, offer to install:
- macOS: `brew install git-cliff`
- Cargo: `cargo install git-cliff`

Check if `cliff.toml` exists in the project root. If not, suggest running `/version-init` to bootstrap configuration.

## Workflow

### 1. Determine Mode

Check if CHANGELOG.md already exists:
- **No CHANGELOG.md**: Generate a full changelog from all history
- **CHANGELOG.md exists**: Prepend only unreleased changes

### 2. Generate Changelog

**Full generation** (new or full regeneration):

```bash
git-cliff --output CHANGELOG.md
```

**Incremental update** (append unreleased changes):

```bash
git-cliff --unreleased --bump --prepend CHANGELOG.md
```

**View latest release section only** (for review):

```bash
git-cliff --latest
```

### 3. Review Output

After generation, read the CHANGELOG.md file and show the user the changes (or the latest section for incremental updates).

If the user wants a full regeneration instead of an incremental update:

```bash
git-cliff --output CHANGELOG.md
```

### 4. Key git-cliff Flags Reference

For comprehensive git-cliff documentation and advanced usage, see [git-cliff Usage Documentation](https://git-cliff.org/docs/category/usage).

| Flag | Purpose |
|------|---------|
| `--output FILE` | Write full changelog to file |
| `--prepend FILE` | Prepend new content to existing file |
| `--unreleased` | Only include unreleased commits |
| `--bump` | Auto-detect next version from commits |
| `--latest` | Show only the latest release section |
| `--tag TAG` | Use a specific tag for the release |
| `--config FILE` | Use a specific config file |
| `--init STYLE` | Initialize cliff.toml (styles: `keepachangelog`, `default`, `minimal`) |
| `--changelog FILE` | Specify custom changelog filename |
| `--strip SECTION` | Strip specified section from changelog |
| `--exclude-path PATTERN` | Exclude paths from changelog |

### 4.1 Configuration Styles

**Keep a Changelog** (recommended for most projects):
```bash
git-cliff --init keepachangelog
```
Produces well-structured changelogs with sections: Added, Changed, Deprecated, Removed, Fixed, Security

**Default git-cliff style**:
```bash
git-cliff --init
```
Uses git-cliff's default configuration format

**Minimal style**:
```bash
git-cliff --init minimal
```
Compact, minimal configuration for simple projects

### 5. Common Scenarios

**First-time changelog for an existing project:**
```bash
git-cliff --output CHANGELOG.md
```

**Update before a release:**
```bash
git-cliff --unreleased --bump --prepend CHANGELOG.md
```

**Preview what would be in the next release:**
```bash
git-cliff --unreleased --bump
```

**Regenerate everything from scratch:**
```bash
git-cliff --output CHANGELOG.md
```

## Output Format

After generating, report:

```
## Changelog Updated

- **Mode**: Full generation | Incremental update
- **File**: CHANGELOG.md
- **Versions covered**: vX.Y.Z - vA.B.C (or "all history")

### Preview (latest section)
[Show the most recent section of the changelog]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
