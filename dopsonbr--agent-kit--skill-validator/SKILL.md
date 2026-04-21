---
name: skill-validator
description: Validate Agent Skills against best practices and specification requirements. Use when reviewing skills, before publishing, or to audit existing skills. Use when this capability is needed.
metadata:
  author: dopsonbr
---

# Skill Validator

Validate Agent Skills for correctness, completeness, and best practices.

## When to Use

- Before committing a new skill
- After editing an existing skill
- To audit all skills in a project
- During code review of skill PRs

## Validation Levels

### 🔴 Errors (Must Fix)

These will cause the skill to fail:

| Check | Rule |
|-------|------|
| `name` exists | Required field in frontmatter |
| `name` format | Lowercase, hyphens only, max 64 chars |
| `name` no reserved | Cannot contain "anthropic" or "claude" |
| `description` exists | Required field in frontmatter |
| `description` length | Max 1024 characters |
| No XML in frontmatter | `name` and `description` cannot contain XML tags |
| SKILL.md exists | Must have main instruction file |

### 🟡 Warnings (Should Fix)

These reduce effectiveness:

| Check | Rule |
|-------|------|
| Description has "when" | Should include trigger conditions |
| Has examples | Should have concrete usage examples |
| Token limit | SKILL.md body should be < 5000 tokens |
| Referenced files exist | All `[text](path)` links should resolve |
| Has purpose section | Should explain what skill does |
| Slash command exists | Corresponding command file should exist |
| Command references skill | Command should reference the skill's SKILL.md |
| agent-kit command prefix | Commands in `content/commands/` must use `ak-` prefix |
| No name conflicts | Command name must not conflict with existing skill names |

### 🟢 Suggestions (Nice to Have)

Polish and completeness:

| Check | Rule |
|-------|------|
| Has related skills | Links to complementary skills |
| Consistent formatting | Headers, code blocks properly formatted |
| Version in metadata | Helps track changes |
| License specified | Clear usage terms |

## Usage

### Validate Single Skill

```
User: Validate the create-plan skill

Claude: [Reads content/skills/plan-create/SKILL.md]
[Runs validation checks]
[Reports results]
```

### Validate All Skills

```
User: Validate all skills in this project

Claude: [Scans .claude/skills/ or content/skills/]
[Validates each skill]
[Produces summary report]
```

### Validate Before Commit

```
User: Check if my new skill is ready to commit

Claude: [Validates skill]
[If errors: lists what to fix]
[If clean: confirms ready]
```

## Validation Process

### Step 1: Parse Frontmatter

```typescript
// Extract YAML frontmatter
const frontmatter = parseFrontmatter(skillMd);

// Check required fields
if (!frontmatter.name) error("Missing 'name' in frontmatter");
if (!frontmatter.description) error("Missing 'description' in frontmatter");
```

### Step 2: Validate Name

```typescript
const name = frontmatter.name;

// Length check
if (name.length > 64) error("Name exceeds 64 characters");

// Format check
if (!/^[a-z0-9-]+$/.test(name)) {
  error("Name must be lowercase letters, numbers, and hyphens only");
}

// Reserved words
if (name.includes("anthropic") || name.includes("claude")) {
  error("Name cannot contain 'anthropic' or 'claude'");
}
```

### Step 3: Validate Description

```typescript
const desc = frontmatter.description;

// Length check
if (desc.length > 1024) error("Description exceeds 1024 characters");

// Trigger words check
const hasTrigger = /use when|use for|when you|if you/i.test(desc);
if (!hasTrigger) warn("Description should include trigger conditions");
```

### Step 4: Check Structure

```typescript
// Token estimate (rough: 4 chars = 1 token)
const bodyTokens = skillBody.length / 4;
if (bodyTokens > 5000) warn(`SKILL.md is ~${bodyTokens} tokens (recommended < 5000)`);

// Check for examples
if (!skillBody.includes("## Example")) {
  warn("No examples section found");
}

// Check for purpose
if (!skillBody.match(/## (Purpose|Overview|What)/)) {
  warn("No purpose/overview section found");
}
```

### Step 5: Validate Links

```typescript
// Find all markdown links
const links = skillBody.matchAll(/\[([^\]]+)\]\(([^)]+)\)/g);

for (const [, text, path] of links) {
  if (path.startsWith("http")) continue; // Skip external links

  const fullPath = resolve(skillDir, path);
  if (!exists(fullPath)) {
    error(`Broken link: ${path} (referenced as "${text}")`);
  }
}
```

### Step 6: Validate Slash Command

```typescript
const skillName = frontmatter.name;

// Determine command location based on skill location
// .claude/skills/{name}/ → .claude/commands/{name}.md
// content/skills/{name}/ → content/commands/ak-{name}.md (with ak- prefix!)
const isAgentKit = skillDir.includes('content/skills');
const commandName = isAgentKit ? `ak-${skillName}` : skillName;
const commandPath = skillDir
  .replace('/skills/', '/commands/')
  .replace(`/${skillName}/`, `/${commandName}.md`);

// Check command exists
if (!exists(commandPath)) {
  warn(`No slash command found at ${commandPath}`);
  if (isAgentKit) {
    warn("agent-kit commands must use 'ak-' prefix: ak-{skill-name}.md");
  }
} else {
  // Verify command references the skill
  const commandContent = read(commandPath);
  if (!commandContent.includes(`@skills/${skillName}/SKILL.md`)) {
    warn("Command does not reference the skill's SKILL.md");
  }
}
```

### Step 7: Validate Command Naming

```typescript
// Check for agent-kit command prefix
if (isAgentKit) {
  const commandFiles = listDir('content/commands/');
  for (const file of commandFiles) {
    if (!file.startsWith('ak-')) {
      warn(`Command ${file} must use 'ak-' prefix for agent-kit distribution`);
    }
  }
}

// Check for command/skill name conflicts
const allSkillNames = listDir(skillsDir).map(d => d.name);
const allCommandNames = listDir(commandsDir).map(f => f.replace('.md', ''));

for (const cmd of allCommandNames) {
  const baseName = cmd.replace(/^ak-/, '');
  // Skip if this is the matching command for a skill
  if (allSkillNames.includes(baseName)) continue;

  // Check for conflicts (same name as different skill)
  if (allSkillNames.includes(cmd)) {
    warn(`Command '${cmd}' conflicts with skill name '${cmd}'`);
  }
}
```

## Output Format

### Single Skill Report

```
╭─────────────────────────────────────────╮
│  Skill Validation: create-plan          │
╰─────────────────────────────────────────╯

✅ PASSED

Errors:   0
Warnings: 1
Info:     2

⚠️  Warnings:
   • Description could include more trigger words

ℹ️  Suggestions:
   • Consider adding 'related skills' section
   • Add version to metadata

Files checked:
   ✓ SKILL.md (156 lines, ~620 tokens)
   ✓ assets/plan-template.md

Command:
   ✓ commands/create-plan.md (references skill correctly)
```

### Multi-Skill Report

```
╭─────────────────────────────────────────╮
│  Skill Validation Summary               │
╰─────────────────────────────────────────╯

Skills checked: 4

✅ create-plan      0 errors, 1 warning
✅ doc-contents     0 errors, 0 warnings
✅ skill-creator    0 errors, 0 warnings
❌ my-broken-skill  2 errors, 3 warnings

Overall: 3 passed, 1 failed
```

## Validation Script

For automated validation, use the script:

```bash
./scripts/validate-skill.ts path/to/skill
```

See [scripts/validate-skill.ts](scripts/validate-skill.ts) for implementation.

## Quick Checklist

Before publishing any skill:

- [ ] `name` is lowercase with hyphens only
- [ ] `description` includes what AND when
- [ ] SKILL.md has examples section
- [ ] All file links resolve correctly
- [ ] Body is under 5000 tokens
- [ ] Slash command exists (e.g., `commands/{skill-name}.md`)
- [ ] Command references `@skills/{skill-name}/SKILL.md`
- [ ] agent-kit commands use `ak-` prefix (e.g., `ak-create-plan.md`)
- [ ] No command/skill name conflicts
- [ ] Tested in fresh Claude Code session

## Integration with skill-creator

When using `/create-skill`, validation runs automatically before finalizing.

## Related Skills

- `skill-creator` - Create new skills (validates on creation)
- `doc-contents` - Document skill directories

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dopsonbr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
