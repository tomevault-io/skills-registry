---
name: skill-publisher
description: Publish and distribute skills to GitHub and npm. Use when user wants to "publish skill", "release skill", "distribute skill", "share skill", or "deploy skill to npm". Handles GitHub repository creation, npm package publishing, and installation testing. Use when this capability is needed.
metadata:
  author: l61
---

<essential_principles>
Publishing a skill involves three stages: preparation, GitHub release, and npm distribution. Each stage has specific requirements and validation steps. Always verify the skill structure before publishing.
</essential_principles>

<intake>
What would you like to do?

1. **Publish new skill** - First-time publish to GitHub and npm
2. **Update existing skill** - Release new version
3. **Check publish readiness** - Validate skill structure
4. **Test installation** - Verify npx and skills CLI work

<commentary>
Wait for user response. Common variations:
- "publish my skill" → Option 1
- "release new version" → Option 2  
- "is my skill ready" → Option 3
- "test if it works" → Option 4
</commentary>
</intake>

<routing>
| Response | Next Action |
|----------|-------------|
| 1, "publish", "new", "first" | Route to workflows/publish-new.md |
| 2, "update", "release", "version" | Route to workflows/update-existing.md |
| 3, "check", "validate", "ready" | Route to workflows/validate-skill.md |
| 4, "test", "verify", "install" | Route to workflows/test-installation.md |
| "all", "full", "complete" | workflows/publish-new.md with full pipeline |
</routing>

<quick_reference>
## Quick Commands

**Validate skill:**
```bash
node scripts/validate.js ./my-skill
```

**Publish to GitHub:**
```bash
cd my-skill
git init
git add .
git commit -m "Initial release"
gh repo create my-skill --public --source=. --push
```

**Publish to npm:**
```bash
npm login
npm publish --access public
```

**Test npx:**
```bash
npx my-package-name --version
```
</quick_reference>

<checklist>
## Pre-Publish Checklist

- [ ] SKILL.md has valid YAML frontmatter
- [ ] name: field matches directory name
- [ ] description: explains what AND when to use
- [ ] No README.md in skill directory (auxiliary)
- [ ] Scripts in scripts/ directory
- [ ] References in references/ directory (if needed)
- [ ] GitHub repo created
- [ ] npm package published
- [ ] Test installation works
</checklist>

<success_criteria>
A successfully published skill:
- Is installable via `npx skills add`
- Has valid SKILL.md recognized by skills CLI
- Has working npm package (if CLI tool included)
- Has clear installation instructions
</success_criteria>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/l61) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
