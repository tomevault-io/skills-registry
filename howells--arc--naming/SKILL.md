---
name: naming
description: | Use when this capability is needed.
metadata:
  author: howells
---

<tool_restrictions>
# MANDATORY Tool Restrictions

## REQUIRED TOOLS:
- **`AskUserQuestion`** — Preserve the one-question-at-a-time interaction pattern for every follow-up question, including exploring names, checking TLDs, and choosing strategies. In Claude Code, use the tool. In Codex, ask one concise plain-text question at a time unless a structured question tool is actually available in the current mode. Keep context before the question to 2-3 sentences max, and do not narrate missing tools or fallbacks to the user.
</tool_restrictions>

# Naming Workflow

Generate and validate project/product name candidates using the naming agent.

## What This Does

1. Reads your project materials (README, package.json, vision doc)
2. Extracts naming seeds (core function, metaphors, audience, differentiators)
3. Generates 8-12 name candidates using tech naming strategies
4. **Checks domain availability** across all Vercel-supported TLDs
5. **Checks GitHub** for popular repos with the same name
6. **Searches for existing products** in the same space
7. Presents ranked recommendations with availability matrix

## Process

### Step 1: Read the Project

**Use Task tool to spawn the naming agent:**
```
Task agent: arc:research:naming
"Read the codebase at [path] and generate name candidates.
Check domain availability, GitHub popularity, and product conflicts.
Present ranked recommendations."
```

The agent handles the full naming process autonomously.

### Step 2: Present Results

The agent returns a structured report. Present it to the user as-is.

### Step 3: Explore Further

If the user likes a name, ask:
```
AskUserQuestion:
  question: "What would you like to do with [name]?"
  header: "Next Steps"
  options:
    - label: "Check more TLDs"
      description: "Search additional TLDs for [name] availability"
    - label: "Generate similar names"
      description: "Create more names using the [strategy] style"
    - label: "Register domain"
      description: "Get the command to register [best available domain]"
    - label: "Done"
      description: "I've picked my name"
```

## Usage

```
/arc:naming                     # Name the current project
/arc:naming ~/Sites/myproject   # Name a specific project
```

## Output

For each recommended name:
- **Strategy used** — Verb, Metaphor, Portmanteau, etc.
- **Why it works** — What makes this name fit the project
- **Domain availability** — Matrix across priority TLDs + all Vercel TLDs with no DNS
- **GitHub conflicts** — Popular repos (1k+ stars) sharing the name
- **Product conflicts** — Existing products in the same space

## When to Use

- Starting a new project and need a name
- Current name feels weak or generic
- Validating an existing name choice
- Rebranding or renaming
- Checking domain availability for a name you like

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/howells) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
