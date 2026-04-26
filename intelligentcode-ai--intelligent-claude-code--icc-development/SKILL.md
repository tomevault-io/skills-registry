---
name: icc-development
description: Development guide for contributing to the Intelligent Claude Code (ICC) framework. Use when working on ICC source code, creating skills, updating hooks, or modifying behaviors. Use when this capability is needed.
metadata:
  author: intelligentcode-ai
---

# ICC Framework Development Guide

This skill provides guidance for contributing to the Intelligent Claude Code framework itself.

## Project Structure

```
intelligent-claude-code/
├── src/
│   ├── skills/          # Distributed skills (main source)
│   ├── hooks/           # Claude Code hooks (enforcement)
│   ├── behaviors/       # Behavioral guidance files
│   └── ...
├── .claude/
│   └── skills/          # Local project skills (symlinks + dev tools)
├── docs/                # Documentation
├── install/             # Installation scripts
└── CLAUDE.md            # Project entry point
```

## Key Principle

**This project IS the ICC framework source.**

- Changes to `src/skills/` affect what users install
- Changes to `src/hooks/` affect enforcement rules
- Local `.claude/skills/` contains symlinks for testing

## Creating New Skills

### Location
- **Distributed skills**: `src/skills/<skill-name>/SKILL.md`
- **Project-local skills**: `.claude/skills/<skill-name>/SKILL.md`

### Skill Structure
```markdown
---
name: skill-name
description: Brief description for skill matching. Include trigger phrases.
---

# Skill Title

Content with sections, examples, and guidance.
```

### Testing Skills Locally

**Setup symlinks** (once per clone):
```bash
cd .claude/skills
ln -sf ../../src/skills/commit-pr commit-pr
ln -sf ../../src/skills/git-privacy git-privacy
# Add more symlinks as needed for skills being developed
```

**Workflow:**
1. Create/modify skill in `src/skills/`
2. Add symlink in `.claude/skills/` if new skill
3. Test by invoking the skill via description matching
4. Commit changes to `src/skills/` (source)

## Modifying Hooks

### Location
`src/hooks/*.js`

### Key Hooks
- `summary-file-enforcement.js` - File placement rules
- `agent-infrastructure-protection.js` - System file protection

### Testing Hooks
Hooks are installed to `~/.claude/hooks/` during installation.
For local testing, manually copy or symlink.

## Adding Behaviors

### Location
`src/behaviors/*.md`

### Guidelines
- Behaviors are always-active structural rules
- Keep minimal - use skills for conditional guidance
- Import in CLAUDE.md with `@./path/to/behavior.md`

## Rollout Process

1. **Develop**: Make changes in `src/`
2. **Test locally**: Use symlinks in `.claude/skills/`
3. **Commit**: Follow conventional commit format
4. **PR**: Create PR for review
5. **Release**: Merge triggers installation update

## Do NOT

- Modify `~/.claude/skills/` directly (that's installed location)
- Commit to main branch directly
- Add AI attribution (git-privacy enforced)
- Create overly broad hooks that block legitimate work

## Naming Conventions

- Skills: `lowercase-hyphenated`
- Hooks: `lowercase-hyphenated.js`
- Behaviors: `lowercase-hyphenated.md`
- Commit types: feat, fix, docs, refactor, test, chore, style, perf

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelligentcode-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
