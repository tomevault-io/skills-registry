---
name: n8n-expert
description: Expert in building, troubleshooting, and optimizing n8n automation workflows. Use when creating new workflows, configuring nodes (Edit Fields, Code, AI Agents), writing JavaScript for transformations, or integrating n8n with external services. Use when this capability is needed.
metadata:
  author: raulisai
---

# n8n Expert Skill

This skill enables the agent to design, build, and optimize professional automation workflows in n8n. It covers everything from basic node configuration to advanced AI-driven agents and system integrations.

## 🎯 Core Principles

**ALWAYS prioritize native nodes over custom code:**
1. **Search for native nodes first:** n8n has 400+ integrations.
2. **Centralize Configuration:** Use a `Set` node (labeled "Set: User Config") at the start of the workflow to define IDs, URLs, and toggles.
3. **Multi-Block Documentation:** Use separate `Sticky Note` nodes for:
    - **Note: Overview:** What the workflow does.
    - **Note: Setup:** Required credentials and database IDs.
    - **Note: Compliance:** n8n best practices followed.
4. **Use Edit Fields (Set):** Prefer this for 90% of data mappings.
3. **Use Code node only as a last resort:** Use it for complex logic, multi-item filtering, or advanced data structures that native nodes can't handle.
4. **Structured Triggers:** Always start with a clear entry point (Webhook, Schedule, Chat, etc.).
5. **Documentation:** Use Sticky Notes to explain workflow logic for humans.

DOcumentacion resources:
- [n8n Documentation](https://docs.n8n.io/)
- [n8n-nodes-base Documentation](https://docs.n8n.io/nodes/n8n-nodes-base/)
- [n8n-nodes-base GitHub](https://github.com/n8n-io/n8n-nodes-base)


# Skill: n8n Management CLI (n8n.bat)

This skill allows you to manage n8n workflows through a simplified command-line interface.

## Setup
The CLI is already configured with an API Key and Base URL in the `.env` file within the workspace.

## Available Commands
- `.\n8n list`: Lists all workflows in the connected n8n instance.
- `.\n8n get <ID>`: Retrieves the JSON structure of a specific workflow.
- `.\n8n create <file.json>`: Creates a new workflow from a JSON file.
- `.\n8n update <ID> <file.json>`: Updates an existing workflow.
- `.\n8n activate <ID>`: Activates a workflow.
- `.\n8n deactivate <ID>`: Deactivates a workflow.
- `.\n8n run <ID> [--data '{"key":"value"}']`: Executes a workflow with optional input data.
- `.\n8n config --api-key <KEY> --url <URL>`: Updates the credentials in `.env`.

## Key Files
- `n8n.bat`: The entry point for commands (Windows).
- `n8n_manager.py`: The Python script handling logic.
- `.env`: Stores `N8N_API_KEY` and `N8N_URL`.

## Best Practices
- Always check `.\n8n list` to find IDs before performing operations.
- Workflows are stored in the `workflow/` directory. Use these files for `create` or `update` commands.
- For automation, use the `.\n8n run` command to trigger workflows via API.


## 📦 Essential Node Reference

- **Chat Trigger:** `@n8n/n8n-nodes-langchain.chatTrigger` (Specifically for LangChain-based AI agents). Use version 1.4+.
- **Service Triggers:** Telegram, Discord, Google Sheets (on change), etc.

### 2. Modern AI & LangChain (The Gold Standard)
- **AI Agent Node:** `@n8n/n8n-nodes-langchain.agent` (Use **v3.1+**).
    - **Telegram Trigger Logic (CRITICAL):** When connecting a Telegram Trigger directly to an AI Agent, you MUST set:
        - `promptType: "define"`
        - `text: "={{ $json.message.text }}"`
    - Without this, the Agent won't receive the user's message as input.
- **Language Model:** `@n8n/n8n-nodes-langchain.lmChatOpenAi` (OpenAI) or `@n8n/n8n-nodes-langchain.lmChatGoogleGemini` (Gemini v1).
- **Memory:** `@n8n/n8n-nodes-langchain.memoryBufferWindow` (v1.3+).
    - **Telegram Pattern:** When using Telegram Trigger, you MUST set `Session ID` to `Specify Below` (`sessionIdType: "customKey"`) and use `{{ $json.message.from.id }}` as the `sessionKey`. Standard Chat Triggers provide `sessionId` automatically, but Telegram does not.
- **Tools (CRITICAL):** MUST use specific `Tool` node types (e.g., `n8n-nodes-base.gmailTool` v2.2+).
- **Connection Logic (MAXIMUM CRITICALITY):**
    - The keys in the `"connections"` object MUST be the exact `"name"` of the source node.
    - Inside the array, the `"node"` property MUST be the exact `"name"` of the target node.
    - For AI/LangChain:
        - Source Port: `ai_languageModel`, `ai_memory`, or `ai_tool`.
        - Target Type: `ai_languageModel`, `ai_memory`, or `ai_tool`.
        - Example: `"Source Node Name": { "ai_tool": [[{ "node": "Target Node Name", "type": "ai_tool", "index": 0 }]] }`.
- **Mapping parameters:** Use `{{ /*n8n-auto-generated-fromAI-override*/ $fromAI(...) }}`.

### 3. Core Workflow Nodes (Native)
- **Data Transformation:** 
    - `n8n-nodes-base.set` (v3.4+): Use for assigning values.
    - `n8n-nodes-base.code` (v2): Use for complex JS.
- **Integration:**
    - `n8n-nodes-base.httpRequest` (v4.2+): Support for `genericCredentialType` and various auth methods.
    - `n8n-nodes-base.googleSheets` (v4.5+): For reading/updating sheets with OAuth2.
- **Control & Logic:**
    - `n8n-nodes-base.if` (v2.2): Use for conditional branching.
    - `n8n-nodes-base.wait` (v1.1): Essential for polling loops.
- **Documentation:**
    - `n8n-nodes-base.stickyNote` (v1): Use for detailed markdown guides within the workflow.

## 🛠️ MCP Integration (The "Agentic" Way)

When interacting with n8n via the `n8n-mcp` server, use these tools to bridge the gap between code and automation:

- **`search_workflows`**: Find existing automation logic by name or description.
- **`get_workflow_details`**: Retrieve the full JSON, nodes, and trigger URLs of a specific workflow.
- **`execute_workflow`**: Trigger a workflow remotely (useful for starting an automation from a chat).
- **`list_workflows`**: Overview of all available automations.

### Workflow Management Patterns
1. **Audit:** Use `get_workflow_details` to verify if a workflow follows best practices (error handling, documentation).
2. **Onboarding:** When asked to "learn" a setup, list and get details of key workflows.
3. **Execution:** After building a logic, use `execute_workflow` to test the end-to-end flow.

## 🛠️ Advanced Patterns & Best Practices

### The "$fromAI" Pattern
When building tools for an AI Agent, use the `$fromAI` expression to map agent decisions to node parameters.
```javascript
// Example for a Gmail tool parameter
"={{ /*n8n-auto-generated-fromAI-override*/ $fromAI('Subject', 'The subject of the email', 'string') }}"
```

### Error Handling
- Use the **Error Trigger** node to catch failures globally.
- Enable **Continue on Fail** on individual nodes where 100% success isn't critical.
- Use **IF nodes** after critical steps to check for success flags.

### Polling Pattern (Async API)
1. **Initiate:** `HTTP Request` to start a job (returns `request_id`).
2. **Loop Start:** `Wait` node (e.g., 60s).
3. **Check Status:** `HTTP Request` using `{{ $json.request_id }}`.
4. **Decision:** `IF` node checking `{{ $json.status }} == 'COMPLETED'`.
   - **False:** Connect back to the `Wait` node.
   - **True:** Proceed to Fetch Result.

### Ritual Design Pattern
1. **Trigger:** `Cron`, `Schedule`, or `Chat Trigger`.
2. **Config:** `Set` node with all user-changeable variables.
3. **Intelligence:** AI Agent or specialized nodes (Notion, Telegram).
4. **Documentation:** 3-block Sticky Note system (Overview, Setup, Compliance).

### Compliance Standards
- **Standardized Labels:** Use clear labels like `Set: User Config`, `IF: Success?`, `Telegram: Notify`.
- **No Hardcoded Keys:** All IDs should come from the Config node or Credentials.
- **Rich Documentation:** Use H2 headers and emojis in notes.

## 🚀 Common Workflow Templates

1. **Personal AI Assistant:** Telegram Trigger -> AI Agent (with Memory + Tools for Gmail/Calendar).
2. **Data Analyst:** Web Chat Trigger -> AI Agent (with Google Sheets Tool).
3. **Event Router:** Webhook -> Switch (by severity) -> Slack/Email/Database.
4. **Scheduled Reporter:** Schedule -> HTTP Request -> Edit Fields -> Gmail Send.

## 📝 Checklists

- [ ] Does a native node exist for this service?
- [ ] Are all expressions using the latest `$json` syntax?
- [ ] Is there error handling for critical API calls?
- [ ] Did I use Sticky Notes to document the "why"?
- [ ] If using AI, is the system prompt clear and the tools correctly mapped with `$fromAI`?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raulisai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
