---
name: agent-scaffold
description: Use this skill when creating a new agent from scratch. Generates the complete file structure across all three layers (Spanish staging, English production, OpenCode deploy) in a single operation. Use when: 'create new agent', 'add agent', 'new bot', or when the user describes a new persona to add.
metadata:
  author: cesarszv
---

# Agent Scaffold: Create New Agent (Full Pipeline)

## Overview

This skill creates a new agent across the entire pipeline in a single operation:
1. Spanish definition in `source/` (staging)
2. English translation in `resources/languages/en/source/` (production)
3. OpenCode file in `.opencode/agents/` (deploy)

This eliminates the manual work of creating files in three locations and ensures consistency from the start.

## The Process

### Step 1: Gather Agent Requirements

Ask the user these questions **one at a time** (stop and wait for each answer):

1. **Name**: "What's the agent's name?" (display name, e.g., "Carmen Marin")
2. **Domain**: "What domain or specialization?" (e.g., databases, frontend, testing)
3. **Personality archetype**: "What personality style?"
   - Options: Mentor (direct, tough-love), Analyst (methodical, data-driven), Therapist (empathetic, diagnostic), Journalist (clear, narrative), Specialist (deep domain expert)
   - Or describe their own
4. **Key tools/technologies**: "What specific tools or technologies should it know?" (e.g., PostgreSQL, Kubernetes)
5. **Bash access**: "Should it be able to run system commands?" (yes/no)
6. **Color preference**: "Any color preference for the UI?" (hex or description, or auto-assign)

### Step 2: Determine File Locations

Given agent name "Example Agent":

```
source/Example Agent/AGENTS.md                            ← Spanish (create)
resources/languages/en/source/Example Agent/AGENTS.md     ← English (create)
.opencode/agents/example-agent.md                         ← OpenCode (create)
```

**Kebab-case rules:**
- Lowercase all characters
- Replace spaces with hyphens
- Replace CamelCase with hyphen-separated
- Remove special characters

**Location rules:**
- Original agents go in `source/` → `resources/languages/en/source/`
- Third-party agents go in `others/` → `resources/languages/en/others/`
- Default is `source/` unless explicitly stated as third-party

### Step 3: Generate Spanish AGENTS.md (Staging)

Use this template, filled with the gathered requirements:

```markdown
---
name: <Agent Name>
description: <Spanish description, 1 line>
mode: subagent
tools:
  write: true
  edit: true
  bash: <true|false>
tags:
  - <tag1>
  - <tag2>
---

## Personalidad
<Agent archetype> con más de 15 años de experiencia en <domain>. <2-3 sentences describing personality, passion, and frustration points based on the archetype>.

## Objetivo
<1-2 sentences about what the agent seeks to achieve. Focus on helping the user learn/improve, not just executing tasks.>

## Idioma
- Entrada en español → <Spanish dialect or style appropriate to the personality>
- Entrada en inglés → <English style appropriate to the personality>

## Tono
<2-3 sentences describing communication style, source of authority, and typical reactions. Include what analogies they use.>

## Filosofía
- <PRINCIPIO 1>: <Explicación breve>
- <PRINCIPIO 2>: <Explicación breve>
- <PRINCIPIO 3>: <Explicación breve>
- <PRINCIPIO 4>: <Explicación breve>
- <PRINCIPIO 5>: <Explicación breve>

## Experiencia
- <Technology/tool category 1>
- <Technology/tool category 2>
- <Technology/tool category 3>
- <Methodology/pattern category>
- <Additional relevant experience>

## Comportamiento
- <Behavioral rule 1: what they reject/push back on>
- <Behavioral rule 2: what they always ask first>
- <Behavioral rule 3: how they explain things>
- <Behavioral rule 4: how they propose solutions>
- <Behavioral rule 5: their diagnostic approach>
- Para <problems/decisions>: (1) <step 1>, (2) <step 2>, (3) <step 3>

## Reglas Técnicas
- NUNCA <critical anti-pattern for this domain>
- Siempre <essential practice>
- Prioriza <primary value> sobre <secondary value>
- Recuerda: <memorable maxim related to the domain>
```

### Step 4: Generate English AGENTS.md (Production)

Translate the Spanish file following the `agent-sync` skill rules:
- Preserve agent name, tool names, technical terms
- Translate section headers using the standard mapping
- Translate body text
- Keep the same structure exactly

### Step 5: Generate OpenCode File (Deploy)

Build the OpenCode file following the `agent-to-opencode` skill rules:

```markdown
---
description: <English description, max 120 chars>
mode: subagent
tools:
  write: true
  edit: true
  bash: <true|false>
permission:
  bash:
    "*": <ask|deny>
color: "<#HEXCOLOR>"
---

<English content from Step 4, without frontmatter>
```

**Color assignment:** If user didn't specify, pick from available colors that don't conflict:
- Check existing agents in `.opencode/agents/` for used colors
- Pick a visually distinct hex color

### Step 6: Create Directories and Write Files

1. `mkdir -p source/<Agent Name>/`
2. `mkdir -p resources/languages/en/source/<Agent Name>/`
3. Write all three files
4. Verify by listing all created files

### Step 7: Run Audit

After creation, mentally run through the `agent-audit` checks:
- [ ] Agent appears in all three layers
- [ ] File structure matches across Spanish and English
- [ ] OpenCode frontmatter is valid
- [ ] No Spanish in English/OpenCode files
- [ ] Color doesn't duplicate an existing agent

## Personality Archetype Reference

These archetypes inform the tone, behavior, and philosophy sections:

### Mentor (tough-love educator)
- **Tone:** Direct, confrontational, no filter
- **Frustration:** Mediocrity, shortcuts, hype without substance
- **Analogies:** Iron Man/Jarvis, construction/architecture
- **Example agents:** Yupi Dupi, GentlemanClaude

### Analyst (methodical investigator)
- **Tone:** Technical, rigorous, data-driven
- **Frustration:** Assumptions without evidence, premature optimization
- **Analogies:** Scientific method, detective work
- **Example agents:** Carmen Marin

### Therapist (empathetic diagnostician)
- **Tone:** Empathetic but direct, non-judgmental but honest
- **Frustration:** Superficial diagnoses, blame culture
- **Analogies:** Psychology, medicine, rehabilitation
- **Example agents:** Sigmund Bot

### Journalist (clear communicator)
- **Tone:** Clear, structured, human
- **Frustration:** Jargon, documentation that documents the obvious
- **Analogies:** Journalism, information architecture
- **Example agents:** Yurnal

### Specialist (deep domain expert)
- **Tone:** Pragmatic, battle-tested, skeptical of hype
- **Frustration:** Hype-driven decisions, ignoring fundamentals
- **Analogies:** Domain-specific (cities for distributed systems, gardens for PKM, etc.)
- **Example agents:** Marty McBot, Diana

## Key Principles

- **All three layers at once** - Never create an agent in only one layer
- **Spanish first** - Write Spanish, then derive English, then derive OpenCode
- **Ask before assuming** - Gather requirements before generating
- **Consistent with existing agents** - Follow the same patterns and quality level
- **Immediately auditable** - After scaffold, the pipeline should pass all audit checks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cesarszv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
