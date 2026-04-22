---
name: customize
description: Create personalized skills and agents based on user's background and needs. Use when user asks to 'customize claude', 'create a skill', 'make an agent for', 'personalize my setup', 'help me automate', or when you notice repetitive workflows that could be automated. Use when this capability is needed.
metadata:
  author: ali
---

# Gear Five Customization

Create personalized skills and agents tailored to the user's specific needs.

## When to Activate

**Explicit triggers:**
- "Customize my Claude setup"
- "Create a skill for X"
- "Make an agent that does Y"
- "Personalize Gear Five for my work"
- "I wish Claude could..."

**Proactive triggers (suggest customization):**
- You notice the user doing the same workflow repeatedly
- User asks for help with domain-specific tasks that could be a skill
- User's work would benefit from specialized agents
- After significant project work, offer to capture patterns as skills

## Quick Customization

For simple requests ("make a skill for X"), create directly:

```
~/.claude/skills/{skill-name}/
├── SKILL.md           # Trigger + quick usage
└── REFERENCE.md       # Detailed docs (if needed)
```

For complex needs, run the full discovery flow via the customization-architect agent.

## Skill Template

```yaml
---
name: skill-name
description: "Clear description with trigger phrases..."
allowed-tools: Read, Bash, etc.
context: fork        # Optional: fork to agent
agent: agent-name    # Optional: which agent to fork to
---

# Skill Title

Quick usage and key patterns.
```

## Agent Template

```yaml
---
name: agent-name
description: "When to use this agent..."
tools: Read, Write, Bash, etc.
model: sonnet        # or opus for complex reasoning
---

System prompt describing the agent's expertise and workflow.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ali) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
