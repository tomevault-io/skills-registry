---
name: skill-creator
description: Interactive guide for creating new Claude Code skills. Walks the user through use case definition, frontmatter generation, instruction writing, folder structure setup, and validation. Use when user says "create a skill", "build a skill", "new skill", "make a skill", "skill-creator", or asks to "scaffold a skill", "generate SKILL.md", or "help me write a skill". Use when this capability is needed.
metadata:
  author: kaankacar
---

# Skill Creator

An interactive, step-by-step guide that helps you create well-structured Claude Code skills following Anthropic's official skill specification.

## Instructions

When the user wants to create a skill, follow this workflow **in order**. Do not skip steps. Ask questions at each step and wait for user input before proceeding.

### Step 1: Gather Use Case Information

Ask the user:

1. **What should this skill do?** Get a clear description of the skill's purpose.
2. **What are 2-3 concrete use cases?** Specific scenarios where the skill would be triggered. Good use case definitions include: trigger phrase, steps, and expected result.
3. **What category does it fall into?**
   - **Document & Asset Creation** - creating consistent output (docs, code, designs, reports). Key techniques: embedded style guides, template structures, quality checklists.
   - **Workflow Automation** - multi-step processes with consistent methodology. Key techniques: step-by-step workflow with validation gates, templates, iterative refinement loops.
   - **MCP Enhancement** - workflow guidance layered on top of an MCP server. Key techniques: coordinates multiple MCP calls, embeds domain expertise, error handling for MCP issues.
4. **Problem-first or tool-first?** Help the user identify their approach:
   - **Problem-first:** User describes an outcome (e.g., "set up a project workspace") and the skill orchestrates the right tools in the right sequence.
   - **Tool-first:** User already has MCP tools connected and the skill teaches Claude the optimal workflows and best practices for using them.
5. **Does it need MCP tools?** If yes, which MCP server(s) and tool names.
6. **Does it need scripts, references, or asset files?** Determine the folder structure needed.
7. **Composability:** Remind the user that Claude can load multiple skills simultaneously. Their skill should work well alongside others, not assume it's the only capability available.

### Step 2: Generate the Skill Name

Derive a name from the user's description. The name **MUST**:

- Be in `kebab-case` (lowercase, hyphens only)
- Have no spaces, underscores, or capitals
- NOT start with "claude" or "anthropic" (reserved)
- Be concise and descriptive (e.g., `sprint-planner`, `code-review`, `report-generator`)

Present the suggested name and ask for confirmation.

### Step 3: Write the Description

Craft a description that includes **all three elements**:

1. **What it does** - the core capability
2. **When to use it** - explicit trigger conditions with example phrases users would say
3. **Key capabilities** - 2-3 specific things it handles

Rules for the description:
- Under 1024 characters
- No XML angle brackets (`<` or `>`)
- Include specific trigger phrases users would actually say
- Mention relevant file types if applicable
- Include negative triggers if the scope could be confused with other skills

Example of a good description:
```
Manages Linear project workflows including sprint planning, task creation, and status tracking. Use when user mentions "sprint", "Linear tasks", "project planning", or asks to "create tickets". Do NOT use for general project discussions without Linear context.
```

Present the draft description and ask for user feedback before continuing.

### Step 4: Define the Folder Structure (Portability Note: skills work identically across Claude.ai, Claude Code, and API)

Based on the use case, propose a folder structure:

```
<skill-name>/
├── SKILL.md            # Required - main skill file
├── scripts/            # Optional - executable code (Python, Bash, etc.)
├── references/         # Optional - documentation loaded as needed
└── assets/             # Optional - templates, fonts, icons used in output
```

Rules:
- `SKILL.md` must be exactly `SKILL.md` (case-sensitive). No variations.
- Do NOT include a `README.md` inside the skill folder.
- The folder name must match the skill name (kebab-case).
- Only include optional directories if the user's use case actually needs them.

### Step 5: Define Success Criteria

Before writing the skill, help the user define how they'll know it's working. These are aspirational targets, not precise thresholds.

**Quantitative metrics:**
- Skill triggers on ~90% of relevant queries (test with 10-20 queries)
- Completes workflow in fewer tool calls than without the skill
- 0 failed API calls per workflow (for MCP skills)

**Qualitative metrics:**
- Users don't need to prompt Claude about next steps
- Workflows complete without user correction
- Consistent results across sessions
- A new user can accomplish the task on first try

Present suggested success criteria and ask the user if they want to adjust them.

### Step 6: Write the SKILL.md Content

Generate the full `SKILL.md` file with this structure:

```markdown
---
name: <skill-name>
description: <the description from Step 3>
metadata:
  author: <ask user or use a default>
  version: 1.0.0
---

# <Skill Display Name>

## Instructions

### Step 1: <First Major Step>
Clear explanation of what happens.
<Include specific tool calls, MCP commands, or actions>

### Step 2: <Next Step>
...

## Examples

### Example 1: <Common scenario>
User says: "<example trigger phrase>"
Actions:
1. <what the skill does>
2. <next action>
Result: <expected outcome>

## Troubleshooting

### <Common error or edge case>
Cause: <why it happens>
Solution: <how to fix>
```

**Best practices to apply when writing instructions:**

- Be specific and actionable, not vague
- Use numbered steps with clear ordering
- Include explicit validation/checks before important actions
- Reference bundled files clearly (e.g., `references/api-guide.md`)
- Put critical instructions at the top with `## Important` or `## Critical` headers
- Keep SKILL.md under 5,000 words - move detailed docs to `references/`
- Include error handling for common failure modes
- For MCP skills: include correct tool names (case-sensitive), expected parameters, and fallback behavior

**Progressive disclosure - the three-level system:**
- **Level 1 (YAML frontmatter):** Always loaded in Claude's system prompt. Provides just enough info for Claude to know when to use the skill.
- **Level 2 (SKILL.md body):** Loaded when Claude thinks the skill is relevant. Contains full instructions.
- **Level 3 (Linked files):** Additional files in `references/`, `scripts/`, `assets/` that Claude discovers only as needed.

Keep the SKILL.md focused on core instructions. Move detailed documentation, API references, and lengthy examples to `references/` and link to them.

**Advanced technique:** For critical validations, consider bundling a script (in `scripts/`) that performs checks programmatically rather than relying on language instructions. Code is deterministic; language interpretation isn't.

**Combating model laziness:** If the skill involves thorough or multi-step work, add a `## Performance Notes` section:
```
- Take your time to do this thoroughly
- Quality is more important than speed
- Do not skip validation steps
```
Note: Adding this to user prompts is more effective than in SKILL.md, but it can help.

**Optional fields to include in frontmatter if relevant:**
- `license`: e.g., `MIT`, `Apache-2.0`
- `allowed-tools`: restrict tool access, e.g., `"Bash(python:*) WebFetch"`
- `compatibility`: environment requirements (1-500 chars)

### Step 7: Create the Files

After the user approves the SKILL.md content:

1. Create the skill folder with the kebab-case name
2. Write the `SKILL.md` file
3. Create any `scripts/`, `references/`, or `assets/` directories and starter files if needed
4. Confirm the file structure to the user

### Step 8: Validate and Test the Skill

Run through this checklist with the user:

**Structure checks:**
- [ ] Folder named in kebab-case
- [ ] `SKILL.md` file exists (exact case)
- [ ] YAML frontmatter has `---` delimiters (opening and closing)
- [ ] `name` field is kebab-case, no spaces, no capitals
- [ ] `name` matches folder name
- [ ] `description` includes WHAT and WHEN
- [ ] `description` is under 1024 characters
- [ ] No XML tags (`<` `>`) in frontmatter
- [ ] No `README.md` inside the skill folder
- [ ] No "claude" or "anthropic" in the skill name

**Content checks:**
- [ ] Instructions are clear and actionable
- [ ] Steps are numbered and ordered
- [ ] Error handling included
- [ ] Examples provided
- [ ] References clearly linked (if applicable)
- [ ] SKILL.md under 5,000 words
- [ ] Detailed docs moved to `references/`
- [ ] Critical instructions appear near the top

**Trigger checks:**
- [ ] Would trigger on obvious task descriptions
- [ ] Would trigger on paraphrased requests
- [ ] Would NOT trigger on unrelated topics
- [ ] Negative triggers included if scope could be confused

**Suggested testing approach:**

1. **Triggering tests:** Suggest 5+ test queries that should trigger and 3+ that should NOT. The user can test these in Claude.
2. **Functional tests:** For MCP skills, verify tool calls succeed and produce expected results.
3. **Performance comparison:** Compare the task with and without the skill - the skill should reduce back-and-forth messages, failed API calls, and total tokens.
4. **Debugging tip:** Ask Claude "When would you use the [skill-name] skill?" - Claude will quote the description back, revealing what's missing.

Report any issues found and offer to fix them.

### Step 9: Provide Next Steps

After creation, tell the user:

1. **To install in Claude Code:** Place the skill folder in the Claude Code skills directory.
2. **To install in Claude.ai:** Zip the folder and upload via Settings > Capabilities > Skills.
3. **To use via API:** Skills can be added to Messages API requests via the `container.skills` parameter. The `/v1/skills` endpoint manages skills programmatically. Note: requires the Code Execution Tool beta.
4. **Organization deployment:** Admins can deploy skills workspace-wide for automatic updates and centralized management.
5. **To test:** Ask Claude a query that should trigger the skill and verify it loads.
6. **To iterate:** Skills are living documents. Watch for these signals:
   - **Undertriggering** (skill doesn't load when it should): Add more keywords and trigger phrases to the description.
   - **Overtriggering** (skill loads for unrelated queries): Add negative triggers, be more specific about scope.
   - **Inconsistent results:** Improve instructions, add error handling, add validation gates.
   - **Pro tip:** Iterate on a single challenging task until Claude succeeds, then extract the winning approach into the skill.
7. **To distribute:** Host on GitHub with a repo-level README (separate from the skill folder), include screenshots and example usage.

## Reviewing Existing Skills

If the user asks to review or improve an existing skill, do the following:

1. Read the existing SKILL.md file
2. Check against the validation checklist (Step 8)
3. Flag common issues: vague descriptions, missing triggers, structural problems
4. Identify potential over/under-triggering risks
5. Suggest test cases based on the skill's stated purpose
6. Offer specific improvements with before/after examples

The user can also bring back edge cases or failures they've encountered: "Use the issues identified in this chat to improve how the skill handles [specific edge case]."

## Patterns Reference

When helping users design their skill, suggest the most appropriate pattern based on their problem-first or tool-first approach:

| Pattern | Use When |
|---|---|
| Sequential Workflow | Multi-step process in specific order |
| Multi-MCP Coordination | Workflow spans multiple services |
| Iterative Refinement | Output quality improves with revision loops |
| Context-Aware Selection | Same outcome, different tools depending on context |
| Domain-Specific Intelligence | Skill adds specialized knowledge beyond tool access |

## Common Mistakes to Watch For

- Description too vague (e.g., "Helps with projects") - must include trigger phrases
- Missing `---` YAML delimiters
- Name with spaces or capitals
- Instructions too verbose - move details to `references/`
- Not including error handling
- Using XML angle brackets in frontmatter
- Including a README.md inside the skill folder
- Naming the skill with "claude" or "anthropic" prefix

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaankacar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
