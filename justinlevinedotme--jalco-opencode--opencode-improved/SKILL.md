---
name: opencode-improved
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Analyze external resources and suggest actionable improvements to OpenCode configuration.

This skill enables systematic improvement by:
1. Analyzing external resources (repos, docs, articles)
2. Extracting relevant patterns and techniques
3. Comparing against current configuration
4. Suggesting concrete, actionable improvements

</overview>

<workflow>

<phase name="resource-acquisition">

## Phase 1: Resource Acquisition

1. **Identify resource type**:
   - GitHub repository → Fetch relevant config files
   - Documentation URL → Extract key concepts
   - Article/blog → Identify patterns and recommendations

2. **Fetch content**:
   - For GitHub: Look for `opencode.json`, `.opencode/`, `AGENTS.md`
   - For docs: Extract configuration examples
   - For articles: Identify actionable recommendations

</phase>

<phase name="pattern-extraction">

## Phase 2: Pattern Extraction

MUST extract patterns in these categories:

| Category | Look For |
|----------|----------|
| Agents | New archetypes, permission patterns, tool configs |
| Skills | Organization patterns, reference structures |
| Commands | Workflow shortcuts, argument patterns |
| Config | Provider setups, MCP configurations, permissions |

</phase>

<phase name="gap-analysis">

## Phase 3: Gap Analysis

MUST compare extracted patterns against:
1. Existing `.opencode/` structure
2. Current `opencode.json` configuration
3. Defined agents, skills, and commands

</phase>

<phase name="recommendations">

## Phase 4: Recommendations

MUST generate recommendations with:
- **Priority**: High / Medium / Low
- **Effort**: Minimal / Moderate / Significant
- **Impact**: Description of benefit
- **Implementation**: Concrete steps or code

</phase>

</workflow>

<output-format>

## Output Format

```markdown
## Analysis Summary

**Source**: [URL or repo name]
**Type**: [Repository | Documentation | Article]
**Relevance Score**: [High | Medium | Low]

## Key Findings

### Finding 1: [Title]

**Pattern**: [What they do]
**Current State**: [What you have]
**Gap**: [What's missing]

### Finding 2: [Title]
...

## Recommendations

### 1. [Recommendation Title] (Priority: High, Effort: Minimal)

**Impact**: [What this improves]

**Implementation**:
\`\`\`jsonc
// Code or configuration example
\`\`\`

### 2. [Recommendation Title] (Priority: Medium, Effort: Moderate)
...

## Not Applicable

These patterns from the source don't apply because:
- [Pattern]: [Reason it doesn't fit]
```

</output-format>

<rules>

## Evaluation Criteria

### Worth Adopting

- SHOULD solve a problem you currently have
- SHOULD improve developer experience
- SHOULD reduce repetitive configuration
- MUST follow OpenCode best practices
- MUST be compatible with existing setup

### Skip If

- Over-engineered for your use case
- Conflicts with existing patterns
- Requires major restructuring for minimal benefit
- Outdated or deprecated approaches
- Not applicable to your workflow

</rules>

<guidelines>

## Opinionated Guidance

This skill is intentionally opinionated:

- **Honesty over politeness**: If nothing is useful, say so
- **Quality over quantity**: Fewer good suggestions > many mediocre ones
- **Practicality over theory**: Focus on actionable improvements
- **Compatibility matters**: MUST NOT suggest breaking changes

### Response Templates

**When useful patterns found:**
> "I found 3 patterns worth adopting and 2 that don't fit your setup. Here's what I recommend..."

**When nothing applicable:**
> "After analyzing [resource], I don't see improvements that would benefit your current setup. The patterns there are [too basic / too specialized / incompatible] because [reason]."

**When partially useful:**
> "One pattern from [resource] could help: [description]. The rest is either already covered or not applicable."

</guidelines>

<examples>

## Integration with Slash Command

This skill works with the `/improve` command:

```markdown
---
description: Analyze a resource and suggest OpenCode improvements
---

Analyze the following resource and suggest improvements to my OpenCode configuration:

$ARGUMENTS

Use the opencode-improved skill. Be opinionated and honest about what's useful.
```

</examples>

<constraints>

## Best Practices

### MUST

- Fetch and analyze the actual content, not guess
- Compare against current configuration specifically
- Provide implementation examples, not just suggestions
- Be honest when nothing is useful
- Prioritize by impact and effort

### MUST NOT

- Suggest changes without understanding current setup
- Recommend patterns that conflict with existing config
- Be vague ("consider improving") - be specific
- Force-fit patterns that don't apply
- Overwhelm with too many suggestions

</constraints>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
