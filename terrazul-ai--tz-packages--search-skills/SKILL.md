---
name: search-skills
description: Searches skillregistry.io for Claude skills and installs them into projects or packages. Use when user wants to find, browse, or install pre-built skills from the skill registry. Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# Search Skills

This skill helps you find and install skills from skillregistry.io, the community registry for Claude Code skills.

## When to Use

Activate this skill when the user wants to:
- Search for available Claude skills
- Find skills for a specific task or domain
- Browse popular or featured skills
- Install a skill from skillregistry.io
- Compare different skills for a use case

## Skill Search Workflow

### 1. Search for Skills

Use WebSearch with the site filter to find skills:

```
site:skillregistry.io [search terms]
```

**Example searches**:
- `site:skillregistry.io code review` - Find code review skills
- `site:skillregistry.io typescript` - Find TypeScript-related skills
- `site:skillregistry.io documentation` - Find documentation skills
- `site:skillregistry.io testing` - Find testing skills

### 2. Present Results

Format search results in a table:

| Skill Name | Description | Author |
|------------|-------------|--------|
| skill-name | Brief description | @author |

Include:
- Skill name (linked if URL available)
- Brief description
- Author/maintainer

### 3. Fetch Skill Details

When user selects a skill, use WebFetch to get details:

```
WebFetch: https://skillregistry.io/skills/[skill-name]
```

Show the user:
- Full description
- Installation instructions
- Required tools/permissions
- Usage examples

### 4. Installation Options

Offer two installation modes:

**Option A: Local Project Installation**
- Location: `.claude/skills/[skill-name]/SKILL.md`
- Best for: Single project use
- Method: Download SKILL.md directly

**Option B: Package Installation**
- Location: `templates/claude/skills/[skill-name]/`
- Best for: Reusable across projects
- Method: Copy skill directory to package

### 5. Perform Installation

**For Local Project**:
1. Create skill directory: `.claude/skills/[skill-name]/`
2. Download/create SKILL.md with skill content
3. Copy any additional files (reference.md, templates/)
4. Verify installation with `ls .claude/skills/`

**For Package**:
1. Create skill directory in package
2. Copy skill files to `templates/claude/skills/[skill-name]/`
3. Verify in package structure

### 6. Post-Installation

After installing:
1. Explain what the skill does
2. Show how to invoke it
3. Note any configuration needed
4. Suggest trying an example command

## Search Tips

### Effective Search Queries

**By Domain**:
- `site:skillregistry.io react` - React development
- `site:skillregistry.io python` - Python development
- `site:skillregistry.io devops` - DevOps skills
- `site:skillregistry.io security` - Security skills

**By Task**:
- `site:skillregistry.io "code review"` - Code review skills
- `site:skillregistry.io "test generation"` - Test generation
- `site:skillregistry.io documentation` - Documentation
- `site:skillregistry.io refactoring` - Refactoring

**By Type**:
- `site:skillregistry.io guidelines` - Standards/guidelines
- `site:skillregistry.io workflow` - Process workflows
- `site:skillregistry.io template` - Template generators

### Browsing Categories

skillregistry.io organizes skills into categories:
- Development (languages, frameworks)
- Quality (testing, review, linting)
- Documentation (API docs, README, comments)
- DevOps (CI/CD, deployment, monitoring)
- Security (audits, vulnerability scanning)

## Reference Materials

For detailed information, consult:
- `reference.md` - Complete search and installation guide
- `examples.md` - Example search sessions

## Tips for Finding Skills

1. **Start Broad**: Use general terms first, then narrow down
2. **Check Reviews**: Look for popular/well-maintained skills
3. **Verify Compatibility**: Ensure skill works with your setup
4. **Read Documentation**: Check usage examples before installing
5. **Test First**: Try skill in a test project first

## Example Invocations

Here are examples of how users might request skill searches:

- "Search for code review skills"
- "Find a skill for generating API documentation"
- "What skills are available for React development?"
- "Install the typescript-guidelines skill"
- "Browse testing skills on skillregistry.io"

## Platform Compatibility

Skills from skillregistry.io are designed for Claude Code. They can also be:
- Used with Codex (shared via `templates/claude/skills/`)
- Converted for Gemini using the `convert-skill-to-gemini` skill

## Common Skills to Consider

### Code Quality
- Code review standards
- Linting guidelines
- Refactoring patterns

### Documentation
- API documentation generators
- README templates
- Code comment standards

### Testing
- Test generation guides
- Testing best practices
- Coverage analysis

### Development
- Language-specific guidelines
- Framework conventions
- Architecture patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
