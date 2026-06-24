---
name: meta-agent
description: Generate OpenCode commands, skills, agents, or plugins when users ask to create/build/scaffold OpenCode components or tooling workflows. Use when deciding between command vs skill vs agent vs plugin. Do not trigger for general OpenCode Q&A or troubleshooting. Use when this capability is needed.
metadata:
  author: digi4care
---

# Meta-Agent: OpenCode Component Generator

I design OpenCode components (commands, skills, agents, plugins) from clear requirements and generate the right files in the right locations.

## When to Use Me

Use me when:

- you need to choose between command, skill, agent, or plugin
- you need architecture decisions for OpenCode components
- you need generation of commands, agents, or plugins
- you need to route skill lifecycle tasks to the right skill

Do not use me for:

- general OpenCode documentation Q&A
- installation or troubleshooting
- non-OpenCode programming questions

## Workflow

1. Analyze the request and extract intent, inputs, outputs, and constraints.
2. Disambiguate "tool" (command vs OpenCode tool in a plugin vs agent tool).
3. Pick the component type using the decision tree reference.
4. If task is skill creation/audit/optimization, route to `skill-creator`.
5. If task is docs-only Q&A, route to `opencode-mastery`.
6. For non-skill component work, gather missing requirements and generate files.
7. Summarize decisions, outputs, and next steps.

## Error Handling

- Missing requirements: ask a targeted question and state the default assumption.
- Ambiguous "tool": ask which meaning is intended before generating files.
- Unknown plugin directory: verify project config; if unclear, ask.
- Risky operations: require explicit confirmation and prefer a dry-run option.
- Wrong lane request: reroute to `skill-creator` or `opencode-mastery` with reason.

## Output Format

- Files created/updated with exact paths
- Short rationale for component choice
- Next steps (tests, setup, or validation)

## Quick Tests

Should trigger:

- "Create a command to format Python files."
- "What should I build: command or plugin for this?"
- "Should this be a skill or an agent?"

Should not trigger:

- "How do I install OpenCode?"
- "Explain MCP docs basics."

Functional:

- "User asks to optimize an existing SKILL.md" -> route to `skill-creator`.
- "Create a plugin that exposes a CSV validation tool." -> keep in meta-agent lane.

## References

- `src/skill/meta-agent/references/decision-tree.mdx`
- `src/skill/meta-agent/references/component-templates.mdx`
- `src/skill/meta-agent/references/paths-and-installation.mdx`
- `src/skill/meta-agent/references/tool-recommendations.mdx`
- `src/skill/meta-agent/references/examples.mdx`
- `src/skill/skill-creator/SKILL.md`
- `src/skill/opencode-mastery/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/digi4care) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
