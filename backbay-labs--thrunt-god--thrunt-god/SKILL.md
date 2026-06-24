---
name: thrunt-list-phase-assumptions
description: Surface the agent's assumptions about a phase approach before planning Use when this capability is needed.
metadata:
  author: backbay-labs
---


<objective>
Analyze a phase and present the agent's assumptions about technical approach, implementation order, scope boundaries, risk areas, and dependencies.

Purpose: Help users see what the agent thinks BEFORE planning begins - enabling course correction early when assumptions are wrong.
Output: Conversational output only (no file creation) - ends with "What do you think?" prompt
</objective>

<execution_context>
@.github/thrunt-god/workflows/list-phase-assumptions.md
</execution_context>

<context>
Phase number: $ARGUMENTS (required)

Project state and huntmap are loaded in-workflow using targeted reads.
</context>

<process>
1. Validate phase number argument (error if missing or invalid)
2. Check if phase exists in huntmap
3. Follow list-phase-assumptions.md workflow:
   - Analyze huntmap description
   - Surface assumptions about: technical approach, implementation order, scope, risks, dependencies
   - Present assumptions clearly
   - Prompt "What do you think?"
4. Gather feedback and offer next steps
</process>

<success_criteria>

- Phase validated against huntmap
- Assumptions surfaced across five areas
- User prompted for feedback
- User knows next steps (discuss context, plan phase, or correct assumptions)
  </success_criteria>

---
> Source: [backbay-labs/thrunt-god](https://github.com/backbay-labs/thrunt-god) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
