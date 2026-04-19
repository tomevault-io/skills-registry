---
name: workflow-agent
description: Execute Tellen workflow agents for multi-step audit procedures. Use this when the user wants to run an automated workflow, list available workflow agents, or retrieve workflow execution results. Use when this capability is needed.
metadata:
  author: tellenai
---

# Workflow Agent Execution

Tellen's workflow agents are pre-built, multi-step AI procedures that automate common audit tasks. Each workflow agent consists of a sequence of steps with defined inputs, outputs, and tools.

## Overview

Workflow agents handle complex audit tasks that require multiple steps and tool usage. Examples include:
- **Engagement letter review** — Analyze an engagement letter against firm standards
- **Analytical procedures** — Perform preliminary or substantive analytical procedures
- **Risk assessment** — Evaluate audit risks based on client data
- **Internal control evaluation** — Assess design and operating effectiveness of controls
- **Going concern analysis** — Evaluate going concern indicators

Each workflow step can use tools like:
- `web_search` — Find external information (regulatory updates, industry data)
- `standards_research` — Look up ASC/GAAS guidance
- `template_filler` — Populate workpaper templates
- `agent_notes` — Record findings for later steps

## How to Use

### Step 1 — Discover Available Workflows

<!-- TODO: Add MCP tool for listing available workflow agents -->
<!-- This requires exposing the workflow agent catalog via MCP -->
<!-- The agent registry (ai_agent/services/agent_registry.py) tracks all agents -->

**Current approach**: Ask the user what they're trying to accomplish. Common workflows:

1. **Data Report / Dashboard** — Generate analytical dashboards from financial data
2. **Standards Research Workflow** — Deep-dive research with structured output
3. **Document Analysis** — Comprehensive review of complex documents

Direct the user to the Tellen UI to browse available workflow agents at **Agents → Browse Workflows**.

### Step 2 — Prepare Inputs

Each workflow agent requires specific inputs. Help the user gather:

1. **Documents** — Upload any required documents using `process_document`
2. **Context** — Engagement details (audit period, materiality, client info)
3. **Parameters** — Workflow-specific settings (e.g., which accounts to analyze, risk thresholds)

### Step 3 — Execute the Workflow

<!-- TODO: Add MCP tool for invoking workflow agent execution -->
<!-- This requires exposing the POST /api/workflow-executions/by-message endpoint via MCP -->
<!-- WorkflowAgent processes steps sequentially, streaming results in real-time -->

**Current approach**: The user executes workflows through the Tellen UI:
1. Navigate to the workflow agent
2. Provide required inputs and upload documents
3. Click **Run** to start execution
4. Monitor step-by-step progress in real-time

Each step produces structured outputs:
- `dataOutputs` — Structured data (tables, figures, calculations)
- `fileOutputs` — Generated documents (workpapers, memos)
- `freeFormOutputs` — Narrative analysis and findings

### Step 4 — Review and Extract Results

<!-- TODO: Add MCP tool for fetching workflow execution results -->
<!-- This requires exposing the execution results endpoint via MCP -->

**Current approach**: After a workflow completes:
1. Review results in the Tellen UI
2. Export generated workpapers and reports
3. Use `search_files` to query specific findings from the workflow's output documents

Help the user interpret the results:
- Summarize key findings from each step
- Flag items that need follow-up
- Identify any exceptions or warnings

## Combining with Other Skills

Workflow results pair well with other Tellen capabilities:

- **Research**: If a workflow flags an unusual transaction, use `/tellen:research` to verify the accounting treatment
- **Substantive Testing**: Workflow outputs can inform which attributes to test — transition to `/tellen:substantive-testing`
- **Document Search**: Use `search_files` to cross-reference workflow findings against uploaded source documents

## Architecture Notes

For developers: Tellen workflow agents use the `WorkflowAgent` class which:
- Inherits from `BaseAgent[WorkflowAgentRequest, WorkflowAgentResponse]`
- Executes steps deterministically in sequence
- Each step has a defined set of available tools
- Supports both "fast" (quick analysis) and "smart" (thorough analysis) response modes
- Results stream in real-time via WebSocket

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tellenai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
