---
name: sync-docs
description: Update and maintain CLAUDE.md and README.md documentation Use when this capability is needed.
metadata:
  author: ruchernchong
---

Update and maintain project documentation for CLAUDE.md and README.md:

## 1. CLAUDE.md Management
**Purpose:** Keep Claude's project instructions current and accurate

- **Scan project structure:** Check for new tools, scripts, build systems
- **Update build commands:** Sync with package.json, Makefile, or build configs
- **Add new patterns:** Document coding conventions, file organization
- **Refresh tool commands:** Update linting, testing, deployment instructions
- **Check environment setup:** Verify installation and setup steps

## 2. README.md Maintenance
**Purpose:** Keep public project documentation up-to-date

- **Update installation:** Check dependencies, requirements, setup steps
- **Refresh usage examples:** Verify code samples and API documentation
- **Validate links:** Test external links, badges, and references
- **Update project status:** Current features, roadmap, version info
- **Check screenshots/demos:** Ensure visual examples are current

## 3. Smart Detection Process

1. **File existence:** Create CLAUDE.md or README.md if missing
2. **Project analysis:** Scan codebase for changes since last update
3. **Cross-reference:** Compare project structure with documented instructions
4. **Outdated sections:** Identify stale information automatically
5. **Consistency check:** Ensure both files complement each other

## 4. Validation Steps

- Run project commands mentioned in docs to verify they work
- Test installation steps from scratch perspective
- Validate all external links and references
- Check that CLAUDE.md instructions match current project setup
- Ensure README.md accurately represents current project state

Focus on keeping both files synchronized with the actual project state and useful for their respective audiences: Claude for CLAUDE.md, users/developers for README.md.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ruchernchong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
