---
name: version-init
description: Bootstrap versioning for a project - detect project type, initialize git-cliff config, and generate initial CHANGELOG.md using semantic versioning Use when this capability is needed.
metadata:
  author: jason-hchsieh
---

# Version Init

You are bootstrapping semantic version management for a project. This skill helps initialize version management following [Semantic Versioning](https://semver.org/) principles (MAJOR.MINOR.PATCH).

## Prerequisites

This skill supports multiple version management approaches. git-cliff is optional.

## Workflow

### 1. Ask User: Choose Version Management Method

Ask the user how they want to manage versions:

1. **CHANGELOG + git-cliff** (Recommended) - Automatic changelog generation from conventional commits
   - Requires: git-cliff installed
   - Features: Auto-detect version bumps, structured changelog

2. **CHANGELOG only** - Manual version tracking in changelog
   - No external dependencies
   - Features: Simple, explicit version history

3. **Git tags only** - Use git tags for version tracking
   - No CHANGELOG.md
   - Features: Lightweight, minimal overhead

4. **Custom approach** - User specifies their own method
   - Features: Flexible, user-defined process

Based on their choice, proceed with the appropriate workflow.

**If git-cliff selected:** Check that git-cliff is installed:

```bash
git-cliff --version
```

If not installed, offer to install:
- macOS: `brew install git-cliff`
- Cargo: `cargo install git-cliff`
- Linux: `apt-get install git-cliff` or package manager

### 2. Detect Project Type

Look for existing project files to determine the project type:

- `package.json` → Node.js / JavaScript / TypeScript
- `Cargo.toml` → Rust
- `pyproject.toml` / `setup.cfg` / `setup.py` → Python
- `go.mod` → Go
- `pom.xml` / `build.gradle` / `build.gradle.kts` → Java / Kotlin
- `mix.exs` → Elixir
- `pubspec.yaml` → Dart / Flutter
- `CMakeLists.txt` → C / C++
- `Chart.yaml` → Helm chart
- `.claude-plugin/plugin.json` → Claude Code plugin

Report what was detected.

### 3. Check Existing Version State

For each detected project file, read and extract the current version (if any). Report what was found:

```
Detected project type: Node.js
Existing versions found:
  - package.json: 0.3.1
  - .claude-plugin/plugin.json: 1.0.0
```

### 4. Determine Initial Version

If existing version files have versions, use the highest one as the starting version.

If no versions exist, ask the user for an initial version. Suggest:
- `0.1.0` for new/early projects
- `1.0.0` for production-ready projects

### 5. Initialize Configuration (Method-Specific)

**If CHANGELOG + git-cliff selected:**

Check if `cliff.toml` already exists. If not, create one:

```bash
git-cliff --init keepachangelog
```

This creates a `cliff.toml` with the [Keep a Changelog](https://keepachangelog.com/) format, which produces well-structured changelogs with categories:
- Added, Changed, Deprecated, Removed, Fixed, Security

If the user prefers a different style, offer alternatives:
- `git-cliff --init` → Default git-cliff style
- `git-cliff --init minimal` → Minimal style

**If CHANGELOG only selected:**

Create a minimal CHANGELOG.md template with the current version.

**If Git tags only selected:**

No configuration files needed - versions are tracked via git tags.

**If Custom selected:**

Ask user what configuration/process they want to set up.

### 6. Generate Initial CHANGELOG.md (if applicable)

**If CHANGELOG + git-cliff selected:**

Generate the initial changelog from existing git history:

```bash
git-cliff --output CHANGELOG.md
```

Show the user the generated changelog.

**If CHANGELOG only selected:**

Create initial CHANGELOG.md with the starting version and instructions for manual updates.

**If Git tags only selected:**

Skip changelog creation (unless user wants one anyway).

### 7. Create Initial Git Tag (Optional)

If no git tags exist yet, offer to create an initial tag:

```bash
git tag v<version>
```

Only do this if the user agrees.

### 8. Sync Version Files (Optional)

If multiple version files were found with different versions, offer to sync them all to the same version using the Edit tool.

## Output Format

```
## Version Init Complete

### Project Type
- Detected: [type]

### Configuration
- cliff.toml: Created (keepachangelog style)
- CHANGELOG.md: Generated from git history

### Version Files
| File | Previous | Current |
|------|----------|---------|
| package.json | 0.3.1 | 0.3.1 (unchanged) |
| plugin.json | 1.0.0 | 1.0.0 (unchanged) |

### Next Steps
- Use `/bump` to increment versions
- Use `/changelog` to update CHANGELOG.md before releases
- Use `/version-check` to validate consistency
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
