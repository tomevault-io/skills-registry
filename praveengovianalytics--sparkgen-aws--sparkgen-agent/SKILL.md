---
name: sparkgen-agent
description: Add, modify, remove, list, or show agents in the workflow Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Agent

Manage agents defined in `config/ai_workflow.yaml`.

## Dynamic Context

Before any action:
1. Read `config/ai_workflow.yaml` — parse the `agents:` section to get current agent list
2. List prompt files: `ls prompts/`
3. List context files: `ls contexts/`
4. List guardrail agent overrides: `ls guardrails/agents/ 2>/dev/null`

## Actions

### List Agents (`/sparkgen-agent list`)
Parse `config/ai_workflow.yaml` and display a table:
| Name | Role | Tools | RAG Mode | Guardrail Sets |
For each agent, show its key configuration.

### Show Agent (`/sparkgen-agent show <name>`)
Display full agent config from workflow YAML including:
- Role and description
- Tools assigned
- Prompt file + context files + variables
- Guardrail sets
- RAG config (mode, knowledge bases, top_k)
- Handoff rules (from `handoffs:` section)

### Add Agent (`/sparkgen-agent add <name>`)
Create all required files for a new agent:
1. **Prompt file**: Create `prompts/<name>.md` with a system prompt template
2. **Context file** (optional): Create `contexts/<name>_guidelines.md` if the agent needs specific context
3. **Guardrail override** (optional): Create `guardrails/agents/<name>.md` for agent-specific rules
4. **Workflow entry**: Add agent config to `agents:` section in `config/ai_workflow.yaml`:
   ```yaml
   - name: <name>
     role: "<role description>"
     description: "<what this agent does>"
     tools: [<tool_list>]
     prompt:
       prompt_file: prompts/<name>.md
       context_files: [contexts/platform_context.md]
       variables:
         agent_name: "<Name>"
       prompt_suffix: ""
       rag_context_template: ""
     guardrails:
       use_sets: [platform_defaults]
     rag:
       enabled: false
       mode: standard
       knowledge_bases: []
       inject_context: false
   ```
5. **Handoff rule**: Add entry to `handoffs:` section:
   ```yaml
   - from: main_agent
     to: <name>
     condition: "intent == '<name_intent>'"
   ```
6. **Update orchestration**: If pattern was `single_agent`, switch to `router_manager`
7. **Validate**: Run `make validate` to verify the workflow loads correctly

### Modify Agent (`/sparkgen-agent modify <name>`)
1. Read current agent config from workflow YAML
2. Ask what to change (role, tools, prompt, guardrails, RAG config)
3. Update the workflow YAML and any related files
4. Run `make validate`

### Remove Agent (`/sparkgen-agent remove <name>`)
1. Remove agent entry from `agents:` in workflow YAML
2. Remove handoff rules referencing the agent from `handoffs:`
3. Optionally remove `prompts/<name>.md` and `contexts/<name>_guidelines.md`
4. If only one agent remains, switch orchestration pattern to `single_agent`
5. Run `make validate`

## Important Notes
- The `main_agent` is the entry point and should not be removed
- Agent names must be snake_case
- Every agent needs at minimum: a prompt file and a workflow YAML entry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
