---
name: skill-builder
description: Create new Cline skills following best practices. Use when the user wants to create a new skill, formalize existing workflows into a skill, or restructure domain knowledge into reusable instructions. Use when this capability is needed.
metadata:
  author: christopherwoodall
---

# Skill Builder

This skill guides you through creating well-structured Cline skills that follow the repository's design patterns and best practices.

## When to Use This Skill

Activate this skill when:
- User explicitly requests creation of a new skill
- User describes a repetitive workflow that should be automated
- User wants to formalize domain expertise into reusable instructions
- User wants to improve or restructure an existing skill

## Core Principles

Before creating a skill, verify it's appropriate:

1. **Progressive Loading**: Skills should contain extensive domain knowledge that would waste context if always active
2. **On-Demand Activation**: Skills trigger based on request matching, not explicit invocation
3. **Institutional Knowledge**: Best skills encode expertise that's hard to document elsewhere
4. **Not Always-On**: If instructions should always apply, use rules instead

## Skill Creation Workflow

### Step 1: Clarify the Skill's Purpose

Ask the user:
- What specific tasks should this skill handle?
- When should this skill activate versus remain dormant?
- What expertise or workflow knowledge needs to be captured?
- Will this need supporting files (templates, scripts, docs)?

### Step 2: Design the Metadata

Craft the YAML frontmatter:

```yaml
---
name: skill-name
description: Concise description of what this skill does and when to use it. This is critical for activation matching. Max 1024 characters.
---
```

**Name Requirements:**
- Must match the directory name exactly
- Use kebab-case (lowercase with hyphens)
- Be descriptive but concise
- Examples: "database-migration", "api-integration", "release-management"

**Description Best Practices:**
- Start with the primary action verb
- Specify concrete trigger conditions
- Include relevant keywords for matching
- Mention file types, technologies, or domains
- Keep under 1024 characters but be thorough

Good description example:
```
Deploy applications to AWS using CDK. Use when deploying, updating infrastructure, managing AWS resources, or troubleshooting cloud deployments. Handles CloudFormation stacks, Lambda functions, S3 buckets, and API Gateway configurations.
```

Bad description example:
```
Helps with AWS stuff
```

### Step 3: Structure the Instructions

Organize the skill content following this hierarchy:

```markdown
# Skill Name

Brief overview of what this skill accomplishes.

## When to Use

Explicit conditions for activation (helps clarify scope).

## Prerequisites

- Required tools or dependencies
- Expected environment setup
- Access requirements

## Core Workflow

### 1. First Major Step

Detailed instructions for the first phase.

- Sub-step with specifics
- Code examples where relevant
- Common pitfalls to avoid

### 2. Second Major Step

Continue with logical progression.

## Best Practices

- Domain-specific conventions
- Quality standards
- Performance considerations
- Security requirements

## Common Issues

### Issue: Specific Problem

**Symptoms:** How to recognize this
**Solution:** Step-by-step resolution
**Prevention:** Avoid future occurrences

## References

Link to supporting files if needed:
- [Setup Guide](docs/setup.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Template](templates/config.yaml)
```

### Step 4: Add Supporting Files (If Needed)

Create additional files only when:
- Instructions would become unwieldy in a single file
- Templates or scripts provide reusable assets
- Advanced documentation serves optional depth

Directory structure:
```
skill-name/
├── SKILL.md              # Required: main instructions
├── docs/                 # Optional: extended documentation
│   ├── setup.md
│   ├── advanced.md
│   └── troubleshooting.md
├── templates/            # Optional: configuration templates
│   ├── config.yaml
│   └── example.json
└── scripts/              # Optional: utility scripts
    ├── validate.py
    └── helper.sh
```

Reference supporting files in instructions:
```markdown
For initial setup, follow [setup.md](docs/setup.md).

Use the template at `templates/config.yaml` as a starting point.
```

### Step 5: Determine Storage Location

Choose where the skill lives:

**Global Skills** (`~/.cline/skills/` or `C:\Users\USERNAME\.cline\skills\`):
- Personal workflow patterns
- Cross-project utilities
- Individual preferences
- Skills you want available everywhere

**Project Skills** (`.cline/skills/` in workspace):
- Team-shared expertise
- Project-specific workflows
- Domain knowledge tied to this codebase
- Skills that should version-control with the project

Default to project skills for team collaboration, global skills for personal productivity.

### Step 6: Create the Skill Files

1. Create the directory with the exact skill name
2. Create `SKILL.md` with frontmatter and instructions
3. Add supporting files if planned
4. Test activation by asking Cline to perform the task

### Step 7: Validate and Refine

After creation, verify:
- [ ] Directory name matches `name` field exactly
- [ ] Description is clear, specific, and under 1024 chars
- [ ] Instructions are detailed enough to execute without clarification
- [ ] Code examples use proper syntax (Python variables in double quotes per user preference)
- [ ] Supporting files are referenced correctly
- [ ] Skill activates on appropriate requests
- [ ] Instructions follow logical progression
- [ ] Common issues are documented

## Code Examples Best Practices

When including code in skills:

**Python:**
```python
# Always use double quotes for variables per user preference
variable_name = "value"
data = {"key": "value"}
result = function_call("argument")
```

**Shell Scripts:**
```bash
#!/bin/bash
# Include error handling
set -euo pipefail

# Add usage documentation
usage() {
    echo "Usage: $0 [options]"
    exit 1
}
```

**Configuration Files:**
- Include comments explaining each section
- Provide sensible defaults
- Note which values must be customized

## Anti-Patterns to Avoid

**Don't:**
- Create skills for simple, one-off tasks (use chat instead)
- Duplicate functionality better served by rules
- Write vague descriptions that won't trigger properly
- Include always-applicable advice (use rules instead)
- Create overly broad skills that do too much
- Forget to specify when NOT to use the skill

**Do:**
- Encode complex, multi-step workflows
- Capture institutional knowledge and best practices
- Use specific trigger conditions in descriptions
- Include error handling and edge cases
- Reference supporting files for depth
- Test activation with realistic requests

## Skill Improvement Prompts

When refining existing skills, consider:

1. **Clarity**: Are instructions unambiguous?
2. **Completeness**: Do they cover edge cases?
3. **Efficiency**: Can supporting files reduce verbosity?
4. **Discoverability**: Will the description trigger appropriately?
5. **Maintainability**: Can future developers update this easily?

## Integration with Other Features

Skills complement other Cline features:

- **Rules**: Skills for on-demand expertise, rules for always-active constraints
- **Workflows**: Skills for domain knowledge, workflows for explicit automation sequences
- **Hooks**: Skills provide instructions, hooks inject custom logic at execution points
- **MCP Servers**: Skills orchestrate MCP tools for complex workflows

## Example Skill Templates

### Simple Skill (Single File)

```markdown
---
name: code-review
description: Perform thorough code reviews using team standards. Use when reviewing pull requests, analyzing code quality, or providing feedback on implementations.
---

# Code Review

Conduct systematic code reviews following team standards.

## Review Checklist

### 1. Correctness
- Logic implements requirements accurately
- Edge cases are handled
- No obvious bugs

### 2. Security
- Input validation present
- No hardcoded secrets
- Proper authentication/authorization

### 3. Performance
- No N+1 queries
- Appropriate caching
- Efficient algorithms

### 4. Readability
- Clear variable names
- Logical organization
- Adequate comments

## Provide Feedback

Structure feedback as:
1. Summary of changes
2. Strengths
3. Issues (critical vs. minor)
4. Recommendations
5. Approval status
```

### Complex Skill (Multiple Files)

```markdown
---
name: database-migration
description: Create and apply database migrations safely. Use when modifying database schemas, adding tables/columns, or evolving data structures. Handles both up and down migrations with rollback procedures.
---

# Database Migration

Safe database schema evolution with rollback capability.

## Prerequisites

- Database connection configured
- Migration tool installed (Alembic, Flyway, etc.)
- Backup capability available

## Migration Process

### 1. Analyze Change Impact

Before creating migration:
- Identify affected tables
- Check for dependent code
- Estimate downtime
- Plan rollback strategy

### 2. Create Migration File

Follow [migration template](templates/migration-template.sql).

### 3. Test Migration

See [testing guide](docs/testing-migrations.md) for procedures.

### 4. Apply to Production

Follow [production deployment](docs/production-deployment.md) checklist.

## Rollback Procedures

Every migration must include rollback logic. See [rollback guide](docs/rollback.md).
```

## Final Checklist

Before considering a skill complete:

- [ ] Metadata is accurate and descriptive
- [ ] Instructions are comprehensive yet concise
- [ ] Code examples follow user preferences (double quotes for Python)
- [ ] Supporting files are organized logically
- [ ] Skill activates on appropriate requests
- [ ] Team members can understand and use the skill
- [ ] Common failure modes are documented
- [ ] Success criteria are clear

## Iteration Strategy

Skills evolve based on usage:

1. **Initial Version**: Get 80% right, ship it
2. **Gather Feedback**: Note where Cline struggles or asks for clarification
3. **Refine**: Add missing details, improve descriptions
4. **Expand**: Add supporting files for advanced scenarios
5. **Prune**: Remove unused sections that waste context

Skills are living documentation—embrace continuous improvement.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/christopherwoodall) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
