---
name: load-workspace-skills-at-session-initialization
description: Guide for loading workspace skills at Copilot session initialization. Use this when implementing or modifying session startup behavior. Use when this capability is needed.
metadata:
  author: markusheiliger
---

## Workspace Skills Loading at Session Initialization

When implementing or modifying Copilot session initialization, MUST automatically load workspace skills from `.github/skills/` directory.

**Loading Behavior:**
- Load all subdirectories containing SKILL.md files at session creation
- Parse SKILL.md content and make available to AI prompts throughout session lifecycle

**DO:**
- Silently skip when `.github/skills/` directory is missing
- Silently skip when `.github/skills/` directory is empty
- Display list of loaded skills when valid SKILL.md files are found
- Require `name` and `description` fields in SKILL.md YAML frontmatter
- Skip malformed SKILL.md files with a warning message (missing required fields, invalid YAML)
- Allow other valid skills to load even when individual skills fail
- Show user feedback during initialization (✓ for success, ⚠ for warnings)

**DON'T:**
- Block session startup due to skill loading failures
- Throw exceptions for missing or empty skills directory
- Require session restart notification for skill changes (document this limitation instead)
- Load skills from directories without SKILL.md files

**User Feedback Format:**
```
Loading workspace skills:
  ✓ authentication/SKILL.md
  ✓ database/SKILL.md
  ⚠ api-design/SKILL.md - Missing required 'description' field
```

**Rationale:** Skills must be consistently available during specification, implementation, and validation phases to guide AI-generated implementations with architectural context from processed ADRs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markusheiliger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
