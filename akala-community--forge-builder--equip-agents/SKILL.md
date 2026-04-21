---
name: equip-agents
description: After architecture design produces an agent roster, research and scaffold the concrete tools each agent needs — MCP servers, direct API integrations, custom tools, or OpenClaw skills — so workspace generation has real tool bindings instead of placeholders. Use when this capability is needed.
metadata:
  author: akala-community
---

# Equip Agents

## Triggers

**Activation phrases and patterns:**

- "equip my agents with tools"
- "what tools do my agents need"
- "set up tools for the team"
- "research APIs for my agents"
- "find MCPs for my system"
- "what integrations do my agents need"
- "tool up the team"
- "research tools for [agent name]"
- "find an API for [capability]"
- "is there an MCP for [service]"

**Trigger logic:**
- Primary triggers: "equip", "tools", "MCP", "API" combined with agent/team/system references
- Contextual triggers: After `design-system` Phase 3 completes (agent roster confirmed) — The Forge detects agents with stated capabilities but no concrete tool bindings and offers to equip them. Also activates when a user asks "how will [agent] actually do [capability]?" during or after architecture design.

---

## Execution Flow

**Overview:** Load the agent roster, map each agent's stated capabilities to concrete tool requirements, research available MCP servers/APIs/tools for each requirement, present recommendations, confirm selections with the user, then scaffold tool configurations and produce a complete tool plan document.

### Step-by-step flow:

1. **Load Agent Roster** — Read the designed agent roster and extract capability requirements
   - Input: Agent roster from `design-system` output or `add-agent` spec, existing workspace config (if any)
   - Output: Agent-capability matrix — each agent mapped to its stated capabilities that require external tools
   - User interaction: "Let me inspect your agent roster and map out what each agent needs to actually deliver on its stated capabilities."

2. **Identify Tool Requirements** — For each agent, decompose stated capabilities into concrete tool needs
   - Input: Agent-capability matrix from step 1
   - Output: Tool requirements list per agent — each requirement categorized as: API access, MCP server, custom tool, or OpenClaw skill
   - User interaction: Present the requirements matrix — "Here's what each agent needs to function. Does this match your expectations, or should I adjust any requirements?"

3. **Research Available Options** — Search for existing solutions matching each tool requirement
   - Input: Tool requirements list
   - Output: Options matrix — for each requirement: 1-3 candidate solutions with type, source, availability, trade-offs
   - User interaction: None during research — The Forge searches autonomously. If a requirement has no good match, flag it for custom tool creation.

   **Research sources (in priority order):**
   - MCP server registries (modelcontextprotocol.io, GitHub MCP repositories)
   - Platform-native APIs (Meta Graph API, Twitter API, etc.)
   - Third-party aggregator APIs (Postiz, Buffer, Hootsuite, etc.)
   - Existing OpenClaw skills in the workspace
   - Open-source tools and libraries
   - Custom tool stub (last resort — when nothing fits)

4. **Present Tool Recommendations** — Show the options matrix to the user with clear trade-offs
   - Input: Options matrix from step 3
   - Output: User-confirmed tool selections per agent
   - User interaction: For each agent, present a table of capabilities → recommended tools with:
     - Tool name and type (MCP/API/custom/skill)
     - Source (registry URL, npm package, API docs link)
     - Availability status (production-ready, beta, requires signup)
     - Trade-offs (rate limits, cost, complexity, reliability)
     - Alternative options if available

   "Here's what I found for each agent. Review and confirm, swap alternatives, or flag anything that needs a custom tool."

5. **Confirm Selections** — Lock in tool choices per agent
   - Input: User feedback on recommendations
   - Output: Final tool selection per agent — locked and ready for scaffolding
   - User interaction: Walk through each agent's selections. If user wants to swap, present alternatives. If user flags a gap, note it for custom tool creation. Confirm the complete selection before proceeding.

6. **Scaffold Tool Configurations** — Generate configuration artifacts for each selected tool
   - Input: Final tool selections
   - Output: Per-tool configuration scaffolds:
     - **MCP servers:** Server config block for openclaw.json with connection URI, auth placeholders, and capability description
     - **Direct APIs:** Environment variable template (.env stub), endpoint reference, authentication setup instructions, rate limit notes
     - **Custom tools:** Tool definition stub with function signature, description, input/output schema, and implementation placeholder
     - **OpenClaw skills:** Skill binding reference for openclaw.json
   - User interaction: "Scaffolding tool configurations. I'll generate the connection stubs and config blocks — you'll need to fill in credentials and connection details for external services."

7. **Produce Tool Plan Document** — Compile the complete agent-to-tool binding map
   - Input: All scaffolded configurations
   - Output: Tool plan document containing:
     - Agent-to-tool binding table (which agent uses which tools)
     - Per-tool setup instructions (how to get API keys, install MCP servers, configure connections)
     - Environment variable checklist (all required secrets/credentials)
     - Tool policy recommendations (which tools each agent should be allowed vs restricted from)
     - Dependency graph (which tools depend on external services, which are self-contained)
     - Integration with `design-system` Phase 4 (how tool bindings feed into workspace file generation)
   - User interaction: "Here's your complete tool plan. This feeds directly into workspace generation — every agent will have real tool bindings, not placeholders."

---

## Instructions

### Role

The Forge activates this skill as a **Tool Procurement Engineer** — the specialist who bridges the gap between what agents are designed to do and what tools actually exist to make it happen. Researches the ecosystem, evaluates options, and delivers concrete configurations. "Every agent needs the right tools on the workbench. Let me stock yours."

### Behavior Guidelines

- **Research-driven:** Don't guess at tool availability — search for it. Use web search to find MCP registries, API documentation, and third-party services. Present findings with source links.
- **Honest about gaps:** If no good tool exists for a requirement, say so clearly. Recommend custom tool creation with a realistic assessment of what that entails.
- **Trade-off transparent:** Every tool choice has trade-offs (cost, rate limits, reliability, complexity). Present them. Don't hide downsides.
- **Agent-centric:** Organize everything by agent. The user thinks in terms of "what does my ContentCreator need?" not "here's a list of MCP servers."
- **Scaffold, don't implement:** Generate configuration stubs and setup instructions. Don't build full API integrations — that's implementation work, not architecture.
- **Collaborative on selections:** Present recommendations, but the user decides. If they prefer a different tool, adjust without pushback.
- **Clarification triggers:** Ask when: a capability maps to multiple equally viable tool types (MCP vs direct API), when the user's preferred platform has limited API access, or when cost is a factor in tool selection.

### Detailed Instructions

#### Phase 1: Roster Analysis

1. Read the agent roster from the most recent `design-system` output or `add-agent` spec.
2. For each agent, extract:
   - Stated capabilities (what the agent is supposed to do)
   - External dependencies (what services/data sources it needs)
   - Interaction patterns (how it communicates with other agents — affects tool sharing)
3. Build the agent-capability matrix:
   | Agent | Capability | External Dependency | Tool Type Needed |
   |-------|-----------|-------------------|-----------------|
4. Present the matrix to the user for validation.
5. If the roster isn't available or is incomplete, ask the user to describe their agents or run `design-system` first.

#### Phase 2: Tool Research

1. For each capability-tool requirement pair, research in this order:
   a. **MCP servers first** — check modelcontextprotocol.io registry, GitHub MCP server repositories, community MCP lists. MCP is the preferred integration path for OpenClaw.
   b. **Platform APIs second** — check if the target platform (Meta, Twitter, etc.) has a developer API. Evaluate: authentication method, rate limits, available endpoints, cost tier.
   c. **Third-party aggregators third** — check services like Postiz, Buffer, Zapier, Make that aggregate platform access. Evaluate: API availability, pricing, feature coverage.
   d. **Existing OpenClaw skills** — check if any existing skill already covers the requirement.
   e. **Custom tool last** — if nothing fits, flag for custom tool creation.

2. For each candidate, collect:
   - Name and version
   - Type (MCP/API/aggregator/skill/custom)
   - Source URL or package name
   - Authentication method (API key, OAuth, bearer token)
   - Rate limits and quotas
   - Cost (free, freemium, paid — with pricing if available)
   - Maturity (production, beta, experimental)
   - Key limitations

3. Rank candidates by: reliability > simplicity > cost > feature coverage.

#### Phase 3: Recommendation Presentation

1. Present findings organized by agent:
   ```
   ## Agent: {agent-name}

   ### {Capability 1}
   **Recommended:** {tool name} ({type})
   - Source: {URL}
   - Auth: {method}
   - Limits: {rate limits}
   - Cost: {tier}
   - Why: {1-sentence rationale}

   **Alternatives:**
   - {alternative 1} — {trade-off vs recommended}
   - {alternative 2} — {trade-off vs recommended}
   ```

2. Flag any capabilities with no good existing solution — clearly state "Custom tool needed" with scope estimate.
3. Flag any capabilities where MCP server exists but is experimental — present maturity assessment.
4. Wait for user to confirm selections per agent before proceeding.

#### Phase 4: Configuration Scaffolding

1. **For each MCP server selected:**
   Generate the MCP server config block. The exact config structure depends on the user's OpenClaw setup (some use `mcpServers` in openclaw.json, others configure via skill entries or environment). Present the standard format:
   ```json
   {
     "mcpServers": {
       "{server-name}": {
         "command": "{install command}",
         "args": ["{args}"],
         "env": {
           "{KEY}": "{placeholder — user must fill}"
         }
       }
     }
   }
   ```
   Plus: installation instructions, credential acquisition guide.
   Also configure tool access — add the MCP tools to the agent's tool policy via `agents.list[].tools.alsoAllow` or `agents.defaults.tools.alsoAllow` (to extend existing tool access without replacing it). If restricting tools, use `agents.list[].tools.deny`.

2. **For each direct API selected:**
   - `.env` stub with required variables:
     ```
     # {Service Name} API
     {SERVICE}_API_KEY=your-api-key-here
     {SERVICE}_API_SECRET=your-api-secret-here
     {SERVICE}_BASE_URL={endpoint}
     ```
   - Endpoint reference: key endpoints the agent will use
   - Auth setup instructions: where to get credentials, how to configure
   - Rate limit notes: requests/minute, daily quotas

3. **For each custom tool needed:**
   - Tool definition stub:
     ```yaml
     name: {tool-name}
     description: {what it does}
     input_schema:
       type: object
       properties:
         {param}: {type and description}
     ```
   - Implementation notes: what the tool needs to do, suggested approach
   - Complexity estimate: simple (wrapper), medium (logic), complex (full integration)

4. **For each OpenClaw skill binding:**
   - Configure in `skills.entries` with skill identifier, `enabled: true`, and any `apiKey`, `env`, or `config` values
   - If the skill should be agent-specific, add the skill identifier to `agents.list[].skills` array
   - Reference `skills.allowBundled` if using bundled skills

#### Phase 5: Tool Plan Assembly

1. Compile the complete tool plan document:
   - **Agent-Tool Binding Table** — master reference of who uses what
   - **Setup Checklist** — ordered list of external services to configure
   - **Environment Variables** — complete list of secrets/credentials needed
   - **Tool Policies** — recommended access controls per agent using `agents.list[].tools` (profile, allow, alsoAllow, deny) — feeds into `harden-workspace`
   - **Dependency Map** — which tools depend on external services, startup order
   - **Integration Notes** — how this tool plan feeds into `design-system` Phase 4 workspace generation

2. Write the tool plan to `{forge_artifacts}/tool-plan-{skillName}.md`.
3. Present summary to the user with next steps.

---

## Data Dependencies

**Required at runtime:**
- Agent roster from `design-system` or `add-agent` output
- Web search capability (for researching MCP registries, API docs, third-party services)
- OpenClaw config schema (for generating valid tool policy and MCP server configuration)
- Existing skill registry (to check for overlapping capabilities)

**Optional enhancements:**
- MCP server registry cache (pre-loaded list of known MCP servers with capabilities)
- Past tool plans from memory (cross-project learning — which tools worked well for similar use cases)
- Platform API status pages (for real-time availability and rate limit information)
- Cost comparison data for competing services

---

## Connected Skills

**Leads to:** `design-system` Phase 4 (workspace file generation with real tool bindings), `harden-workspace` (tool policy hardening with concrete tool list), `setup-knowledge` (knowledge tools may be identified during equip)
**Triggered by:** `design-system` Phase 3 (agent roster confirmed — The Forge offers to equip before generating workspace files), `add-agent` Phase 2 (new agent spec confirmed — offer to equip the new agent), standalone user request
**Shares context with:** `design-system` (agent roster, workspace config), `harden-workspace` (tool policies feed from equip selections), `setup-knowledge` (memory/knowledge tools may overlap)

---

## Success Criteria

- Every agent in the roster has its stated capabilities mapped to concrete tool requirements
- Each tool requirement has at least one researched solution (MCP, API, custom, or skill)
- User confirmed tool selections per agent
- Configuration scaffolds generated for every selected tool (MCP configs, API stubs, custom tool definitions)
- Tool plan document produced with binding table, setup checklist, and environment variable list
- No agent left with placeholder or unresolved tool bindings
- Tool policies recommended for integration with `harden-workspace`
- Clear handoff path to `design-system` Phase 4 for workspace generation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akala-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
