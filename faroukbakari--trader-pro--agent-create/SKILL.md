---
name: agent-create
description: Create VS Code custom agents (.agent.md files) with proper YAML frontmatter, constraint craft, skill integration, and handoff design. Use when creating agents, configuring subagents, designing handoff chains, or writing effective constraints. Use when this capability is needed.
metadata:
  author: faroukbakari
---

# Custom Agent Creation Techniques

Techniques for authoring high-quality VS Code Custom Agent files (`.agent.md`). This skill teaches **craft** — how to make good design choices during agent creation — not workflow (the creating agent owns that).

---

## <constraints>

### CRITICAL
- **File location**: `.github/agents/{name}.agent.md` (user-facing) or `{name}.sub.agent.md` (subagent)
- **No code fence wrappers** — written agent files start directly with `---` (raw text). Read-back tools may display fences around file content as rendering artifacts — ignore those.
- **Valid tool aliases**: `read`, `search`, `edit`, `execute`, `agent`, `web/fetch`, `web`, `todo`

### IMPORTANT
- Use XML-style sections for Claude optimization (see `<agent_prompt_structure>`)
- Apply constraint hierarchy: CRITICAL > IMPORTANT > GUIDELINES
- Reference templates from `.github/agents/{type}-template.md` for full structure

### GUIDELINES
- Keep agent files under 200 lines — extract to skills if larger
- Use generic archetypes in examples, not specific agent names

</constraints>

---

## <yaml_reference>

### Required Properties

Every agent file must have:

```yaml
---
name: agent-name              # Lowercase, kebab-case, matches filename
description: What this agent does. Use when {trigger scenarios}.
---
```

### Conditional Properties — Decision Table

Include each property ONLY when the condition applies:

| Property | Include When | Skip When | Format |
|----------|-------------|-----------|--------|
| `model` | Agent needs a specific model tier | Default model is acceptable | `Claude Sonnet 4.5 (copilot)` |
| `tools` | Agent needs a restricted tool set | Agent should have all tools | `['read', 'search', 'edit']` |
| `agents` | Agent delegates to subagents | No delegation needed | `['research', 'verify']` |
| `user-invokable` | Subagent — set to `false` | User-facing (default `true`) | `false` |
| `handoffs` | Agent participates in workflow chains | Standalone agent | See `<handoff_design>` |
| `argument-hint` | Agent benefits from input guidance | Purpose is obvious from name | `"describe the feature"` |
| `disable-model-invocation` | Agent should never auto-activate | Agent can be auto-routed | `true` |

### Tool Alias Reference

| Alias | Maps To | Use Case |
|-------|---------|----------|
| `read` | `read_file`, `list_dir`, notebook tools | File inspection |
| `search` | `grep_search`, `file_search`, `semantic_search` | Code discovery |
| `edit` | `replace_string_in_file`, `create_file`, `multi_replace_string_in_file` | Code modification |
| `execute` | `run_in_terminal`, `get_terminal_output`, `await_terminal`, `kill_terminal`, `create_and_run_task` | Command execution |
| `agent` | `runSubagent` | Task delegation |
| `web/fetch` | `fetch_webpage` | Web content retrieval |
| `playwright/*` | `browser_click`, `browser_snapshot`, `browser_navigate`, etc. | Browser automation (Playwright MCP) |
| `todo` | `manage_todo_list` | Progress tracking |
| `context7/*` | `query-docs`, `resolve-library-id` | Versioned external library documentation |
| `filesystem/*` | `fs_batch_operations`, `fs_search_files`, etc. | Workspace-confined filesystem ops |
| `skillsmp/*` | `skillsmp_search`, `skillsmp_install_skill`, etc. | Skills marketplace |
| `mcp-registry/*` | `list_servers_v0`, `get_server_version`, etc. | MCP server registry |

**⚠️ MCP toolset glob rule**: All MCP toolsets **MUST** use the `/*` glob suffix (e.g., `'context7/*'`, NOT `'context7'`). Bare toolset names without `/*` produce VS Code warnings and may not resolve correctly.

**Least-privilege principle**: Start with the minimum tool set. Add tools only when the agent genuinely needs them.

</yaml_reference>

---

## <frontmatter_decision_tree>

Construct the YAML frontmatter systematically:

```
1. NAME & DESCRIPTION (always required)
   └── name: kebab-case matching filename
   └── description: purpose + "Use when" trigger keywords

2. DEPLOYMENT MODE
   ├── User-facing? → file: {name}.agent.md (user-invokable defaults true)
   └── Subagent?    → file: {name}.sub.agent.md
                    → user-invokable: false (mandatory)
                    → No handoffs (SA-6)
                    → No agents list (SA-7)

3. MODEL SELECTION
   ├── Mostly reads/searches?           → Haiku 4.5
   ├── Writes code or reviews?          → Sonnet 4.5
   ├── Orchestrates or plans?           → Opus 4.6
   └── Subagent?                        → Default one tier below parent (SA-2)
   Format: "Claude {Model} {Version} (copilot)"

4. TOOL SET (least privilege)
   ├── Read-only agent?                 → ['read', 'search']
   ├── + needs web fetch?               → add 'web/fetch'
   ├── + needs browser automation?      → add 'playwright/*'
   ├── + needs external lib docs?       → add 'context7/*'
   ├── + writes code?                   → add 'edit'
   ├── + runs commands?                 → add 'bash'
   ├── + delegates?                     → add 'agent'
   └── + tracks progress?              → add 'todo'
   ⚠️ MCP toolsets ALWAYS use '/*' suffix: 'context7/*', 'filesystem/*', never bare 'context7'

5. DELEGATION (optional)
   ├── Spawns subagents?                → agents: ['name1', 'name2']
   └── No delegation?                   → omit agents

6. WORKFLOW TRANSITIONS (user-facing only)
   ├── Part of a chain?                 → add handoffs (see <handoff_design>)
   └── Standalone?                      → omit handoffs
```

</frontmatter_decision_tree>

---

## <agent_prompt_structure>

### Why XML Sections Work

Claude processes `<section_name>` tags as **semantic boundaries**, like HTML structures a document. Benefits:

- **Instruction following**: Content within named sections receives higher-priority attention
- **Selective retrieval**: Sections create natural "attention anchors" — the model targets `<constraints>` for rules, `<methodology>` for approach
- **Debuggability**: When an agent misbehaves, you can pinpoint which section failed

### Recommended Layout

```markdown
# Role Title

You are a **[Role]** that [purpose].

---

## <constraints>

### CRITICAL
- **VERB-FIRST RULE** — explanation

### IMPORTANT
- **VERB-FIRST RULE** — explanation

### GUIDELINES
- **VERB-FIRST RULE** — explanation

</constraints>

---

## <methodology>

### Phase 1: {Name}
[Phased approach, not flat lists]

</methodology>

---

## <output_format>

[Expected output structure]

</output_format>
```

### Section Naming Conventions

| Section | Purpose | Required? |
|---------|---------|-----------|
| `<constraints>` | Behavioral rules with hierarchy | Yes |
| `<methodology>` | Phased approach to tasks | Yes |
| `<output_format>` | Expected deliverable structure | Recommended |
| `<project_rules>` | Project-specific conventions | When applicable |
| `<caller_protocol>` | How parents invoke (subagents only) | Subagents: mandatory (SA-4) |
| `<anti_patterns>` | Common mistakes to avoid | Recommended |

</agent_prompt_structure>

---

## <constraint_craft>

Writing effective constraints is the highest-leverage skill in agent design. Weak constraints are the #1 quality problem.

### The 4 Properties of a Good Constraint

| Property | Test | Bad | Good |
|----------|------|-----|------|
| **Verb-first** | Starts with action verb? | "Generated code should be typed" | "**NEVER** use `any` type" |
| **Testable** | Can you verify compliance? | "Be careful with edits" | "**ALWAYS** run tests after file changes" |
| **Scoped** | Clear boundary? | "Don't break things" | "**NEVER** edit files in `*_generated/` directories" |
| **Actionable** | Agent knows what to do? | "Follow best practices" | "**MUST** use `make` targets instead of raw `npm`/`poetry`" |

### Constraint Strength Test

Before finalizing, evaluate each constraint:

```
1. If the agent ignores this, what breaks?
   → Nothing?              → GUIDELINE at most, or delete
   → Quality degrades?     → IMPORTANT
   → Incorrect/harmful?    → CRITICAL

2. Could I write a test for this constraint?
   → Yes → Good constraint
   → No  → Too vague, rewrite

3. Does this constraint duplicate another?
   → Yes → Merge or delete
```

### Constraint Anti-Patterns

| Anti-Pattern | Example | Fix |
|---|---|---|
| **Vague mandate** | "Be thorough" | Delete — adds no information |
| **Unbounded scope** | "Always check everything" | Scope it: "Always check type errors before committing" |
| **Contradictory pair** | "Never modify files" + "Fix all errors" | Resolve: "Never modify generated files; fix errors in source only" |
| **Aspirational fluff** | "Strive for excellence" | Delete — constraints must be binary pass/fail |
| **Disguised tool instruction** | "Use grep_search to find patterns" | Move to methodology — constraints define WHAT, methodology defines HOW |

</constraint_craft>

---

## <skill_integration>

### Examples & Quick Reference

See companion files for concrete good/bad examples:
- [Prompt examples](../../agents/templates/prompt-examples.md) — VS Code variables, boundary guidance, thin vs bloated prompts
- [Skill examples](../../agents/templates/skill-examples.md) — Portability patterns, scope guidance, directory structure

### How Agents Reference Skills

Skills integrate into agents through two mechanisms:

#### 1. Inline Reference (in constraints or methodology)

```markdown
### IMPORTANT
- Apply `design-review` skill when validating architectural decisions
- Apply `terminal-usage` skill before running terminal commands
```

The agent reads the skill's SKILL.md via `read_file` when the skill becomes relevant. The skill description (in `copilot-instructions.md` or `<skill>` tag) triggers the load.

#### 2. Skill Tag Registration (in copilot-instructions.md)

```markdown
<skill>
<name>debug-hypothesis</name>
<description>Hypothesis-driven debugging. Use when investigating root cause.</description>
<file>.github/skills/debug-hypothesis/SKILL.md</file>
</skill>
```

### Progressive Disclosure Levels

| Level | What Loads | Cost | When |
|---|---|---|---|
| **L1: Description** | Name + 1-line description | ~20 tokens | Always (base instructions) |
| **L2: Full skill** | SKILL.md body | ~200-500 tokens | Description matches task |
| **L3: Resources** | Supplementary files | Variable | Skill explicitly references them |

### Inline vs Extract Decision

```
Skill content < 5 lines?          → Inline in agent (not worth a file)
Skill content 5-30 lines?         → Inline if agent-specific; extract if reusable
Skill content > 30 lines?         → Always extract to skill file
Used by 2+ agents?                → Always extract to skill file
```

### Writing Good Skill Descriptions

The description is the trigger mechanism — optimize for **recall** (catches relevant tasks) without over-broad **precision**:

```markdown
<!-- ❌ Low recall -->
<description>For testing</description>

<!-- ❌ Low precision -->
<description>For development work</description>

<!-- ✅ Balanced -->
<description>Test planning, coverage analysis, and test pattern selection.
Use when designing test strategies or analyzing test gaps.</description>
```

</skill_integration>

---

## <handoff_design>

### Handoff Mechanics

Handoffs create **lateral transitions** between peer agents. The user sees a button and chooses whether to transition.

```yaml
handoffs:
  - label: "Review Changes"        # Button text shown to user
    agent: review                   # Target agent
    prompt: "Review all changes made in this session."  # Context passed
    send: false                     # false = user confirms, true = auto-submit
```

### `send: true` vs `send: false`

| Setting | Behavior | Use When |
|---|---|---|
| `send: false` | User sees prompt, can edit before sending | **Default** — gives user control |
| `send: true` | Auto-submits to target agent immediately | Well-defined, low-risk transitions only |

### Common Workflow Chains

| Chain | Pattern | When |
|---|---|---|
| Analysis → Planning → Execution | study → plan → implement | New features, complex changes |
| Planning → Execution → Validation | plan → implement → review | Planned work with quality gate |
| Execution → Testing → Review | implement → test → review | Implementation-focused work |
| Diagnosis → Fix → Verify | implement (debug-hypothesis) → review | Bug fixing |

### Handoff Anti-Patterns

| Anti-Pattern | Problem | Fix |
|---|---|---|
| **Circular handoffs** | A → B → A creates infinite loops | Design directional chains |
| **Too many handoffs** | 5+ buttons overwhelm users | Max 2-3 handoffs per agent |
| **Vague prompt** | "Continue the work" gives no context | Specific: "Implement the plan from the analysis above" |
| **Subagent with handoffs** | Subagents don't interact with users (SA-6) | Remove — use parent's handoffs instead |

</handoff_design>

---

## <anti_patterns>

### Agent File Anti-Patterns

| Anti-Pattern | Signal | Fix |
|---|---|---|
| **Monolith agent** | 300+ lines, does everything | Split: extract skills, add subagent delegation |
| **Tool hoarder** | Every tool alias included "just in case" | Audit: remove tools the agent never uses |
| **Flat methodology** | "1. Do this. 2. Do that." | Restructure into named phases with clear purpose |
| **Constraint soup** | 20 constraints, no hierarchy | Classify CRITICAL/IMPORTANT/GUIDELINES; delete fluff |
| **Template copy** | Agent is mostly template with placeholders filled | Customize: add domain knowledge, real constraints |
| **Skill-ignorant** | Reinvents methods that exist as skills | Search existing skills; reference them |

### Naming Anti-Patterns

| Anti-Pattern | Example | Fix |
|---|---|---|
| **Verb-noun mismatch** | `code-writer.agent.md` | Use role noun: `implement.agent.md` |
| **Too generic** | `helper.agent.md` | Name the specialization: `review.agent.md` |
| **Missing `.sub` marker** | Subagent named `research.agent.md` | Rename: `research.sub.agent.md` |

</anti_patterns>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faroukbakari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
