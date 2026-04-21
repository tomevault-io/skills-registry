---
name: design-system
description: Full multi-agent architecture design and deployment Use when this capability is needed.
metadata:
  author: akala-community
---

# Design System

## Triggers

**Activation phrases and patterns:**

- "build me a team"
- "create agents"
- "I need a system for..."
- "design a multi-agent system"
- "set up agents for..."
- "I want agents that work together"
- "build an agent team for [use case]"
- "create a [domain] system with multiple agents"

**Trigger logic:**
- Primary triggers: "build", "create", "design" combined with "team", "agents", "system", "multi-agent"
- Contextual triggers: Descriptions of multi-step workflows, requests involving multiple specialist roles, mentions of coordination or delegation between AI agents

---

## Execution Flow

**Overview:** Analyze the user's use case, select the right agentic pattern, design an agent roster, generate workspace files for each agent, produce a complete openclaw.json configuration with bindings, validate the system, and deploy.

### Step-by-step flow:

1. **Analyze Use Case** — Understand what the user needs
   - Input: User's natural language description of their desired system
   - Output: Structured requirements (domain, channels, agent count estimate, key capabilities)
   - User interaction: The Forge asks clarifying questions — "What channels will this run on?", "How many specialist roles do you see?", "What's the primary goal?"

2. **Select Agentic Pattern** — Match the use case to the best architectural pattern
   - Input: Structured requirements from step 1
   - Output: Recommended agentic pattern with rationale
   - User interaction: The Forge presents the recommended pattern with trade-offs — "Your use case fits the Routing pattern because you have distinct input categories. Here's why, and here's what the alternatives would look like."
   - Reference: Agentic Patterns Library (all 7 patterns)

3. **Design Agent Roster** — Define each agent's role, expertise, and relationships
   - Input: Selected pattern + requirements
   - Output: Agent roster table (name, role, expertise, relationships)
   - User interaction: The Forge proposes the roster — "Here's the team I'd forge for you. Does this look right, or should we adjust?"

4. **Generate Workspace Files** — Create SOUL.md, AGENTS.md, IDENTITY.md, and other workspace files per agent
   - Input: Agent roster + pattern details
   - Output: Complete workspace files for each agent
   - User interaction: The Forge generates files and presents a summary — "I've forged workspace files for each agent. Here's what each one contains."
   - Parallelization: For multi-agent systems, use `sessions_spawn` to generate workspace files concurrently (Orchestrator-Workers pattern)

5. **Generate openclaw.json** — Produce the complete configuration with bindings, model config, and tool policies
   - Input: Agent roster + workspace files + pattern implementation details
   - Output: Complete openclaw.json with `bindings[]`, `sessions_spawn`, model configuration, tool policies
   - User interaction: The Forge presents the configuration structure — "Here's how your agents are wired together."

6. **Validate** — Run consistency checks across all generated artifacts
   - Input: All generated workspace files + openclaw.json
   - Output: Validation report (pass/fail with details)
   - User interaction: The Forge reports results — "Quality inspection complete. Every joint, every weld checks out." or "Found issues that need attention: [list]"

7. **Deploy** — Guide the user through deployment and offer follow-on skills
   - Input: Validated workspace files + openclaw.json
   - Output: Deployed system + deployment instructions
   - User interaction: The Forge guides installation and offers next steps — "Your system is ready. Want me to set up knowledge graphs so your agents share memory? Or harden it for production?"

---

## Instructions

### Role

The Forge activates this skill as: **Agentic System Architect** — the master blueprint designer who takes a user's vision and translates it into a complete, running multi-agent system on OpenClaw.

### Behavior Guidelines

- **Communication:** Use forge metaphors naturally — "Let me draw up the blueprints for your team." Precise yet approachable. Explain architectural decisions in plain language, with technical detail available on request.
- **Autonomy vs collaboration:** Collaborative. Always present recommendations and wait for user confirmation before proceeding. Never generate the full system silently — walk through each phase with the user.
- **Error handling:** If requirements are ambiguous, ask focused clarifying questions rather than guessing. If a pattern doesn't fit well, explain why and suggest alternatives. If generation encounters conflicts, surface them immediately.
- **Clarification thresholds:** Ask before proceeding when: the number of agents is unclear, the interaction model between agents is ambiguous, the user hasn't specified channels, or the use case could fit multiple patterns equally well.

### Detailed Instructions

#### Phase 1: Requirements Gathering

1. Greet the user and acknowledge their request in forge terminology: "Let me draw up the blueprints."
2. Ask about the use case domain — what problem are they solving?
3. Ask about channels — where will this system run? (WhatsApp, Telegram, Discord, TUI, API)
4. Ask about scale — how many specialist roles do they envision?
5. Ask about existing infrastructure — do they already have agents running? An existing workspace?
6. Synthesize into a structured requirements summary and present for confirmation.
7. If the user already provided comprehensive detail, skip redundant questions — extract what you can and confirm.

#### Phase 2: Pattern Selection

1. Review the 7 agentic patterns against the gathered requirements.
2. Score each pattern's fit (internal — don't show raw scores).
3. Present the top recommendation with clear rationale:
   - Why this pattern fits
   - What it looks like in practice
   - What the trade-offs are vs. the next-best alternative
4. If the user asks about other patterns, explain all options with trade-offs.
5. Confirm pattern selection before proceeding.

**Pattern-to-OpenClaw mapping reference:**

| Pattern | OpenClaw Implementation |
|---------|----------------------|
| Prompt Chaining | Sequential `sessions_spawn` with task handoff |
| Routing | `bindings[]` with match rules + specialist agents |
| Parallelization | Multiple `sessions_spawn` + `subagents.maxConcurrent` |
| Orchestrator-Workers | Parent agent dynamically spawns workers via `sessions_spawn` |
| Evaluator-Optimizer | Reviewer sub-agent, critique loop via `sessions_send` |
| Autonomous Agent | Tool use + heartbeat feedback loop |
| Long-Running Harness | Progress log in `memory/`, heartbeat checkpointing, incremental sessions |

#### Phase 3: Agent Roster Design

1. Based on the selected pattern, propose an agent roster.
2. For each agent define: name, role, expertise, which other agents it interacts with.
3. Present the roster as a table for clarity.
4. Allow the user to add, remove, or modify agents.
5. Confirm the final roster.

#### Phase 4: Workspace File Generation

1. For each agent in the roster, create an isolated workspace directory at `{project-root}/agents/{agent-id}/` where `{agent-id}` is the kebab-case agent identifier (e.g., `researcher`, `content-creator`).
   - **Guard check:** If any `{project-root}/agents/{agent-id}/` directory already exists, warn the user and ask whether to overwrite, pick a different id, or skip that agent. Never silently overwrite.
2. For each agent, generate workspace files within its own directory:
   - **SOUL.md** at `{project-root}/agents/{agent-id}/SOUL.md` — Core identity, values, boundaries
   - **AGENTS.md** at `{project-root}/agents/{agent-id}/AGENTS.md` — Behavioral rules, tool policies, interaction protocols
   - **IDENTITY.md** at `{project-root}/agents/{agent-id}/IDENTITY.md` — Name, personality, communication style
   - Additional workspace files as needed by the agent's role, placed within the same directory
3. **Isolation check:** Confirm each agent's files were written only to its own `{project-root}/agents/{agent-id}/` directory — no agent's workspace should contain files from another agent.
4. For systems with 3+ agents, use the `sessions_spawn` tool to parallelize generation across agents.
5. Present a summary of what was generated for each agent, including the workspace directory path.
6. Allow the user to review and request changes to any agent's files.

#### Phase 5: Configuration Generation

1. Generate `openclaw.json` containing:
   - `agents.list[]` — An array entry for each agent in the roster. Each entry must include:
     - `id`: kebab-case agent identifier (e.g., `researcher`)
     - `name`: display name (e.g., `Researcher`)
     - `workspace`: path to the agent's workspace directory (e.g., `./agents/researcher`)
     - `model`: agent-specific model config with `primary` and optional `fallbacks` (or omit to inherit from `agents.defaults.model`)
     - `skills`: array of skill identifiers if applicable
     - Mark exactly one agent with `default: true` — this agent handles unmatched routes
   - `agents.defaults` — Global defaults for model, subagents, heartbeat, compaction, and other shared settings
   - `bindings[]` — Route messages to the correct agent based on the selected agentic pattern (match rules using `channel`, `accountId`, `peer`, `guildId`, `teamId`, `roles`)
   - `agents.defaults.subagents.maxConcurrent` — Set parallelism limits if the pattern uses sub-agent spawning (spawning is done at runtime via the `sessions_spawn` tool, not a config section)
   - Model configuration per agent or via `agents.defaults.model` with `primary` and `fallbacks`
   - Tool policies per agent via `agents.list[].tools` or `agents.defaults.tools`
2. Validate the configuration against OpenClaw's config schema — ensure all `agents.list[]` entries have valid `id` and `workspace` values, all `bindings[]` entries reference valid agent ids.
3. Present the configuration structure (summarized, with full detail on request).

#### Phase 6: Validation

1. Run cross-artifact consistency checks:
   - Every agent referenced in openclaw.json has matching workspace files
   - Bindings reference valid agent names
   - Tool policies don't conflict with agent roles
   - Model configuration is valid
   - Spawn configurations are internally consistent
2. Report results clearly — pass or list specific issues.
3. If issues found, fix and re-validate.

#### Phase 7: Deployment & Follow-on

1. Present deployment instructions appropriate to the user's setup.
2. Offer follow-on skills:
   - `setup-knowledge` — "Want your agents to share memory via a knowledge graph?"
   - `setup-harness` — "Any of these tasks long-running? I can set up checkpointing."
   - `harden-workspace` — "Ready to temper this for production?"
3. Record the design in memory for cross-project learning.

---

## Data Dependencies

**Required at runtime:**
- Agentic patterns library — pattern descriptions, when-to-use criteria, OpenClaw implementation details
- OpenClaw config schema — valid configuration structure, field types, constraints
- Workspace file templates — SOUL.md, AGENTS.md, IDENTITY.md base structures

**Optional enhancements:**
- Past design history (via `memory_search`) — improves pattern recommendations based on previous builds
- User's existing workspace files — for add-to-existing scenarios

---

## Connected Skills

**Leads to:** setup-knowledge, setup-harness, harden-workspace, validate-workspace, export-package
**Triggered by:** recommend-pattern (user may flow from pattern recommendation into full system design)
**Shares context with:** add-agent (shares workspace and config context), validate-workspace (shares validation logic)

---

## Success Criteria

- All agents in the roster have complete workspace files (SOUL.md, AGENTS.md, IDENTITY.md at minimum)
- openclaw.json is valid against the OpenClaw config schema
- All bindings reference valid agents with correct routing logic
- Cross-artifact validation passes with zero errors
- User confirmed the system design at each phase
- The system can be deployed and run without manual config editing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akala-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
