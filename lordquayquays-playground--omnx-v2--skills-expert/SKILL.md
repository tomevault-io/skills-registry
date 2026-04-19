---
name: skills-expert
description: This skill should be used when users ask about creating Claude Code skills, understanding skill structure, writing SKILL.md files, organizing skills with progressive disclosure, or working with the skills framework. Specifically useful for questions about skill frontmatter, supporting files (scripts/references/assets), skill activation patterns, or converting documentation into skills. Use when this capability is needed.
metadata:
  author: lordquayquays-playground
---

# Claude Code Skills Expert

Expert knowledge of the Claude Code skills system for extending Claude's capabilities with specialized knowledge, workflows, and tool integrations.

## When To Use

Invoke when users:
- Ask "how do I create a skill?" or "how do skills work?"
- Want to understand skill structure or YAML frontmatter
- Need examples of existing skills from Anthropic's repository
- Ask about organizing skills with references, scripts, or assets
- Want to convert documentation or repositories into skills
- Mention personal vs project vs plugin skills
- Need help with skill activation patterns or descriptions
- Ask about progressive disclosure or context management
- Want to package or distribute skills

## About Skills

Skills are modular, self-contained packages that extend Claude's capabilities by providing specialized knowledge, workflows, and tools. Think of them as "onboarding guides" for specific domains or tasks—they transform Claude from a general-purpose agent into a specialized agent equipped with procedural knowledge.

**What Skills Provide:**
1. **Specialized workflows** - Multi-step procedures for specific domains
2. **Tool integrations** - Instructions for working with specific file formats or APIs
3. **Domain expertise** - Company-specific knowledge, schemas, business logic
4. **Bundled resources** - Scripts, references, and assets for complex tasks

## Quick Start: Creating a Skill

Basic steps to create a skill:

1. **Create directory**: `~/.claude/skills/my-skill/`
2. **Create SKILL.md** with required frontmatter:
   ```yaml
   ---
   name: my-skill-name
   description: This skill should be used when [specific triggers and use cases]
   ---
   ```
3. **Add instructions**: Write clear, imperative-form instructions
4. **Add supporting files** (optional):
   - `references/` - Documentation loaded on-demand
   - `scripts/` - Executable helper scripts
   - `assets/` - Templates, images, boilerplate
5. **Test activation**: Use the skill in conversation
6. **Iterate**: Refine based on usage

## Anatomy of a Skill

Every skill consists of a required SKILL.md file and optional bundled resources:

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (required)
│   │   ├── name: (required, lowercase-with-hyphens)
│   │   └── description: (required, determines activation)
│   └── Markdown instructions (< 5K words)
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation loaded into context as needed
    └── assets/           - Files used in output (templates, icons, fonts)
```

### Required: SKILL.md

**Metadata Quality:** The `name` and `description` in YAML frontmatter determine when Claude will use the skill. Be specific about what the skill does and when to use it.

**Key principles:**
- **name**: Lowercase-with-hyphens, matches directory name
- **description**: Be specific! Use third-person (e.g., "This skill should be used when...")
- **Body**: Imperative form (verb-first instructions like "To accomplish X, do Y")
- **Length**: Keep under 5K words - use references/ for detailed content

### Optional: Scripts (`scripts/`)

Executable code for tasks requiring deterministic reliability or repeatedly rewritten logic.

**When to include:**
- Same code is being rewritten repeatedly
- Deterministic reliability needed (e.g., PDF rotation, data processing)
- Complex operations better handled by scripts

**Benefits:**
- Token efficient
- Deterministic execution
- May be executed without loading into context

**Examples from Anthropic skills:**
- `pdf/scripts/fill_fillable_fields.py` - Fill PDF forms
- `docx/scripts/document.py` - Document processing utilities

### Optional: References (`references/`)

Documentation and reference material loaded into context as needed to inform Claude's process.

**When to include:**
- Documentation Claude should reference while working
- Large reference material (> 10K words)
- Information that doesn't need to be in main SKILL.md

**Best practice:** If files are large, include grep patterns in SKILL.md to help find content

**Examples:**
- `references/api_docs.md` - API specifications
- `references/schema.md` - Database schemas
- `references/policies.md` - Company policies

**Avoid duplication:** Information should live in either SKILL.md OR references, not both

### Optional: Assets (`assets/`)

Files not intended for context, but used within the output Claude produces.

**When to include:**
- Templates to be copied or modified
- Images, icons, fonts for branding
- Boilerplate code or starter projects

**Examples:**
- `assets/logo.png` - Brand assets
- `assets/template.pptx` - PowerPoint templates
- `assets/frontend-template/` - HTML/React boilerplate

## Progressive Disclosure System

Skills use three-level loading to manage context efficiently:

1. **Metadata** (always loaded): name + description (~100 words)
2. **SKILL.md body** (when triggered): < 5K words
3. **Bundled resources** (as needed): Unlimited*

*Scripts can execute without context; references loaded on-demand

**Best practice:** Keep SKILL.md lean, move exhaustive details to `references/`

## Reference Materials

Comprehensive documentation available in references/:

- **`references/skills.txt`** (3.2M) - Complete Anthropic skills repository
  - Example skills across all categories
  - Skill creation guide from Anthropic
  - Community patterns and best practices
  - Plugin structures

### Finding Content in Large References

Use grep patterns to locate specific content:

```bash
# Find skill creation guidance
grep -n "Skill Creation Process" references/skills.txt

# Find example skills
grep -n "^FILE:.*SKILL\.md$" references/skills.txt | head -30

# Find specific skill examples
grep -n "skill-creator\|mcp-server\|pdf\|docx" references/skills.txt

# Find progressive disclosure details
grep -n "progressive.*disclosure\|Progressive.*Disclosure" references/skills.txt

# Find best practices
grep -n "best.*practice\|Best.*Practice" references/skills.txt
```

## Skill Creation Process

Follow this process in order (from Anthropic's skill-creator):

### Step 1: Understand with Concrete Examples

Skip only if usage patterns are already clearly understood.

**Ask users:**
- "What functionality should this skill support?"
- "Can you give examples of how this skill would be used?"
- "What would a user say that should trigger this skill?"

**Goal:** Clear sense of functionality before building

### Step 2: Plan Reusable Contents

Analyze each example to identify what resources would be helpful:

**Questions to ask:**
1. How would Claude execute this from scratch?
2. What scripts would prevent rewriting the same code?
3. What reference documentation would be helpful?
4. What templates or assets would speed up the work?

**Output:** List of scripts/, references/, assets/ to create

### Step 3: Initialize the Skill

Use the init_skill.py script (if available) or create directory manually:

```bash
mkdir -p ~/.claude/skills/my-skill/{scripts,references,assets}
```

Create SKILL.md with frontmatter template.

### Step 4: Edit the Skill

**Remember:** Writing for another Claude instance to use.

**Start with resources:**
1. Create scripts/ files first
2. Add references/ documentation
3. Include assets/ templates
4. Delete example files not needed

**Update SKILL.md to answer:**
1. What is the purpose? (few sentences)
2. When should the skill be used? (specific triggers)
3. How should Claude use it? (reference all resources)

**Writing style:** Imperative form (verb-first), not second person
- ✅ "To accomplish X, do Y"
- ✅ "Use this when..."
- ❌ "You should do X"
- ❌ "Users can do Y"

### Step 5: Test and Iterate

**Iteration workflow:**
1. Use skill on real tasks
2. Notice struggles or inefficiencies
3. Identify improvements to SKILL.md or resources
4. Implement changes and test again

## Common Patterns

### Pattern: Reference-Based Knowledge Skill

**Use when:** Creating skill from large documentation

**Structure:**
1. Main SKILL.md: Overview + when to use + how to reference docs
2. Large reference file in `references/` (e.g., framework docs, API specs)
3. Grep patterns for finding content
4. Examples of common queries

**Real examples from this repository:**
- anthropic-sdk-expert (2.6M SDK docs)
- langgraph-expert (27M framework docs)
- openai-cookbook-expert (333M examples)

**SKILL.md structure (~200 lines):**
- When To Use (specific triggers)
- Quick Start (common workflow)
- Core Concepts (overview only)
- Reference Materials (with grep patterns)
- Common Patterns (2-3 detailed examples)
- Best Practices
- Troubleshooting

### Pattern: Procedural Workflow Skill

**Use when:** Multi-step processes need guidance

**Structure:**
1. Main SKILL.md: Step-by-step procedures
2. References: Detailed specifications or schemas
3. Scripts: Helper automation for repetitive tasks
4. Assets: Templates or boilerplate

**Real examples from Anthropic:**
- mcp-server (guide for creating MCP servers)
- skill-creator (guide for creating skills)

**SKILL.md structure:**
- Overview of workflow
- Step 1 → Step 2 → Step 3 (detailed)
- Decision trees for complex choices
- References to scripts and templates

### Pattern: Tool Integration Skill

**Use when:** Working with specific tools/APIs/file formats

**Structure:**
1. Main SKILL.md: Tool overview + usage patterns
2. References: API documentation, schemas
3. Scripts: Integration helpers or converters
4. Assets: Config templates or examples

**Real examples from Anthropic:**
- pdf (PDF manipulation with forms, extraction, merging)
- docx (Word document creation and editing)
- xlsx (Excel spreadsheet handling)

**SKILL.md structure:**
- When To Use
- Quick Start workflow
- Core operations (create, read, update)
- Common patterns with examples
- Troubleshooting section

## Writing Effective Descriptions

Description determines activation - be specific!

**❌ Too vague:**
```yaml
description: Expert knowledge of LangChain
```

**✅ Specific with triggers:**
```yaml
description: This skill should be used when users ask about LangChain chains, retrievers, prompts, vector stores, or building RAG applications. Specifically useful for questions about LCEL, document loaders, or LangChain integrations. Note: For stateful agent graphs, prefer langgraph-expert.
```

**Include in description:**
- Primary use cases and scenarios
- Trigger keywords users might say
- Specific features or components covered
- When NOT to use (if there are related skills)
- Cross-references to related skills

## Best Practices

### Keep SKILL.md Lean
- Maximum 5K words (~250 lines max)
- Move exhaustive details to `references/`
- Use bullet points and clear sections
- Link to supporting files explicitly

### Use Imperative Form
- ✅ "To create X, do Y"
- ✅ "Use this skill when..."
- ✅ "Configure the setting by..."
- ❌ "You should create X"
- ❌ "Users can do Y"
- ❌ "It would be helpful to..."

### Organize References Effectively
- Split by topic (core-concepts.md, api-reference.md, examples.md)
- Add grep patterns for large files (> 10K words)
- Document what each reference contains
- Keep filenames descriptive and consistent

### Test Activation Thoroughly
- Try various trigger phrases users might say
- Ensure skill activates when needed
- Check it doesn't activate incorrectly
- Refine description based on testing

### Structure for Clarity
Choose structure that fits the skill's purpose:

**Workflow-Based** (for sequential processes):
- Overview → Decision Tree → Step 1 → Step 2 → Step 3

**Task-Based** (for tool collections):
- Overview → Quick Start → Task Category 1 → Task Category 2

**Reference/Guidelines** (for standards):
- Overview → Guidelines → Specifications → Usage

**Capabilities-Based** (for integrated systems):
- Overview → Core Capabilities → Feature 1 → Feature 2

## Troubleshooting

### Skill Not Activating

**Symptoms:** Claude doesn't load skill when expected

**Solutions:**
- Make description more specific with trigger keywords
- Add examples of what users might say
- Ensure name matches directory exactly
- Verify YAML frontmatter format (no tabs, proper spacing)
- Check for typos in skill name

### Context Too Large

**Symptoms:** Skill consumes too many tokens

**Solutions:**
- Move content from SKILL.md to references/
- Use grep patterns for large reference files
- Keep SKILL.md under 5K words
- Review progressive disclosure strategy
- Consider splitting into multiple focused skills

### Skill Activating Incorrectly

**Symptoms:** Skill loads for unrelated queries

**Solutions:**
- Make description more specific and narrow
- Add "when NOT to use" clause
- Differentiate from similar skills
- Test with edge cases
- Reduce overly broad trigger keywords

### Skills Conflicting

**Symptoms:** Multiple similar skills activating together

**Solutions:**
- Add clear differentiation in descriptions
- Use "Note: For X use Y-expert instead" pattern
- Make each skill's scope more specific
- Consider consolidating related skills

## Example Usage

**User Query:** "How do I create a skill for my team's API documentation?"

**Process:**
1. Activate skills-expert skill ✓
2. Ask clarifying questions about the API
3. Review core structure above
4. Reference `references/skills.txt` for examples
5. Recommend structure:
   - SKILL.md: Overview + how to use API + common patterns
   - references/api-docs.md: Full API specification
   - scripts/test-api.py: Testing helper (if needed)
   - assets/config-template.json: Config example (if needed)
6. Provide complete SKILL.md implementation
7. Include activation testing approach

## Advanced Topics

For advanced skill development, consult references/skills.txt:

- **Custom tool restrictions** with `allowed-tools`
- **Creating skill plugins** for distribution
- **Multi-skill coordination** strategies
- **Dynamic skill loading** patterns
- **Skill versioning** and updates
- **Team collaboration** workflows
- **Packaging skills** with package_skill.py script

## Skill Types

### Personal Skills (~/.claude/skills/)
- Located in user's home directory
- Available across all projects
- Good for personal workflows, preferences

### Project Skills (.claude/skills/)
- Located in project repository
- Shared via version control
- Good for team-specific knowledge

### Plugin Skills
- Distributed via plugin marketplace
- Installable by multiple users
- Good for general-purpose capabilities

See `references/skills.txt` for detailed examples and implementation patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lordquayquays-playground) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
