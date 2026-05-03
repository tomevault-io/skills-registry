---
name: forge-methodology
description: This skill should be used when the user asks to "create a skill", "forge a skill", "generate a skill", "build a Claude skill", "make a new skill", or wants to understand skill creation methodology. Provides research-grounded methodology for creating high-quality Claude Code skills with official documentation verification. Use when this capability is needed.
metadata:
  author: vamfi
---

# Skill Forge Methodology

Create research-grounded, high-quality Claude Code skills by following a rigorous methodology that verifies all information against official documentation before generation.

## Core Principle: Research Before Generation

**NEVER generate skill content from training data alone.** Every piece of guidance in a skill must be:
1. Verified against official documentation
2. Cited with source URLs
3. Marked if based on inference rather than direct documentation

## Research Pipeline

### Stage 1: Claude Code Documentation Research

Before creating any skill, research the official skill format:

1. **DeepWiki Query**: Use `mcp__deepwiki__ask_question` on `anthropics/claude-code` for:
   - Current SKILL.md format requirements
   - Frontmatter fields and their purposes
   - Progressive disclosure patterns

2. **WebFetch Official Sources**:
   - `https://github.com/anthropics/skills` — Official skills repository
   - `https://www.anthropic.com/news/skills` — Skills announcement
   - `https://github.com/anthropics/claude-code/blob/main/plugins/README.md` — Plugin documentation

3. **Context7 Library Docs**: If the skill involves a library, use `mcp__context7__resolve-library-id` then `mcp__context7__get-library-docs` for accurate API information.

### Stage 2: Domain Research

For the specific domain the skill covers:

1. **Identify Authoritative Sources**:
   - Official project documentation
   - GitHub repositories
   - RFC/specification documents

2. **WebSearch for Current Information**:
   - Search: `"[domain] official documentation 2024 2025"`
   - Search: `"[domain] best practices site:[official-domain]"`

3. **WebFetch and Extract**:
   - Read multiple authoritative pages
   - Extract specific procedures, not general overviews
   - Note version numbers and dates

### Stage 3: Synthesis

Combine research into skill content:
- Cross-reference findings across sources
- Resolve conflicts by preferring official sources
- Mark uncertain information clearly

## SKILL.md Format (Official)

Based on official Anthropic documentation:

```yaml
---
name: skill-name-kebab-case
description: This skill should be used when the user asks to "[trigger phrase 1]", "[trigger phrase 2]", "[trigger phrase 3]". [Brief description of capability].
version: 1.0.0
---
```

### Description Requirements

The `description` field is **critical** — it determines when Claude activates the skill.

**Must include:**
- Third-person voice: "This skill should be used when..."
- Specific trigger phrases users would say
- Concrete scenarios, not vague capabilities

**Good example:**
```
This skill should be used when the user asks to "deploy to Kubernetes", "create K8s manifests", "configure kubectl", or mentions Kubernetes deployment patterns.
```

**Bad example:**
```
Helps with Kubernetes tasks.
```

### Body Requirements

Write the SKILL.md body in **imperative/infinitive form** (verb-first):

**Correct:** "Create the deployment manifest with..."
**Incorrect:** "You should create the deployment manifest..."

Keep SKILL.md body **lean** (1,500-2,000 words). Move detailed content to `references/`.

## Directory Structure

```
skill-name/
├── SKILL.md                 # Required: Core instructions (lean)
├── references/              # Detailed documentation
│   ├── patterns.md          # Common patterns and examples
│   ├── api-reference.md     # API details if applicable
│   └── sources.md           # Citation URLs
├── examples/                # Working code examples
│   └── example-usage.sh
└── scripts/                 # Utility scripts
    └── validate.sh
```

## Progressive Disclosure

Skills load in three levels to optimize token usage:

| Level | Content | When Loaded | Size Target |
|-------|---------|-------------|-------------|
| 1 | name + description | Always | ~100 words |
| 2 | SKILL.md body | When triggered | <2,000 words |
| 3 | references/, examples/ | As needed | Unlimited |

**Keep SKILL.md lean.** If content exceeds 2,000 words, move to references/.

## Quality Checklist

Before finalizing any skill, verify:

**Structure:**
- [ ] SKILL.md exists with valid YAML frontmatter
- [ ] `name` and `description` fields present
- [ ] All referenced files exist

**Description Quality:**
- [ ] Uses third-person ("This skill should be used when...")
- [ ] Includes 3-5 specific trigger phrases
- [ ] Lists concrete scenarios

**Content Quality:**
- [ ] Body uses imperative form
- [ ] Body under 2,000 words
- [ ] Detailed content in references/
- [ ] Examples are complete and working

**Accuracy:**
- [ ] All facts verified against official docs
- [ ] Source citations included
- [ ] Research date documented
- [ ] Uncertain content marked

## Source Credibility Hierarchy

Prioritize sources in this order:

1. **Official documentation** (highest trust)
   - *.anthropic.com, official project docs

2. **Official GitHub repositories**
   - anthropics/*, project official repos

3. **Authoritative technical sources**
   - MDN, framework official docs, RFCs

4. **Verified community sources** (use with caution)
   - Stack Overflow (high votes), official blogs

5. **General web** (lowest trust)
   - Mark as "community guidance" if used

## Additional Resources

For detailed patterns and implementation guides, consult:

- **`references/skill-patterns.md`** — Common skill patterns from official examples
- **`references/research-guide.md`** — Detailed research methodology
- **`references/quality-checklist.md`** — Comprehensive validation criteria
- **`references/sources.md`** — Official documentation URLs

## Security Considerations

From official Anthropic guidance:

> "While this makes them powerful, it also means that malicious skills may introduce vulnerabilities... We recommend installing skills only from trusted sources."

When creating skills:
- Never include hardcoded credentials
- Avoid instructions that connect to untrusted external sources
- Document all external dependencies
- Review bundled scripts for security issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vamfi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
