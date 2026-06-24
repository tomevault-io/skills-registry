---
name: create-mcp-server
description: 使用 `mcpheroctl` CLI 通过 MCPHero 平台创建、部署和管理 MCP（Model Context Protocol）服务器。当用户需要构建 MCP 服务器、部署封装 API 或数据库的工具、自动化 MCP 服务器的创建过程，或者通过 MCPHero 将 AI 客户端（如 Claude Desktop、Cursor 等）连接到自定义工具时，可以使用此技能。 Use when this capability is needed.
metadata:
  author: AgentWorkers
---
# 使用 MCPHero 创建 MCP 服务器

[MCPHero](https://mcphero.app) 允许代理**自行构建工具**。代理无需在每次运行时都消耗令牌来处理 API 模式、SQL 查询或输出解析，而是只需创建一次永久性的 MCP 服务器，并之后持续使用该服务器。原本需要 50,000 个令牌的集成操作，现在只需消耗 50 个令牌即可完成。

本文档介绍了使用 **mcpheroctl CLI** 从头到尾构建 MCP 服务器的完整工作流程。

**生产 API 基本 URL：** `https://api.mcphero.app/api`

---

## 先决条件

在使用本功能之前，用户必须已安装并登录 **mcpheroctl**。

### 安装 mcpheroctl

```bash
# via Homebrew (macOS/Linux)
brew install arterialist/mcpheroctl/mcpheroctl

# via uv (cross-platform)
uv tool install mcpheroctl
```

### 登录

1. 登录到 [MCPHero 仪表板](https://mcphero.app)。
2. 转到 **设置** → **组织** → **开发者**。
3. 点击 **创建 API 密钥** 并复制生成的令牌。
4. 运行以下命令：

```bash
mcpheroctl auth login --token <YOUR_ORG_TOKEN>
```

### 验证

```bash
mcpheroctl auth status
```

---

## 向导流程

mcpheroctl CLI 的操作遵循以下线性流程：在任何异步步骤之后，都需要先检查 `wizard_state` 的值是否为 `"idle"`，然后再继续执行后续操作。

```
1. create-session          → Returns server_id (save it, needed everywhere)
2. conversation (loop)     → Gather requirements; stop when is_ready: true
3. start                   → Transition to tool suggestion (async → poll)
4. list-tools              → Review AI-suggested tools
5. refine-tools (optional) → Iterate on tools until satisfied (async → poll)
6. submit-tools            → Confirm selection (deletes unselected tools)
7. (auto env var suggest)  → Triggered automatically after submit-tools (async → poll)
8. list-env-vars           → Review suggested env vars
9. refine-env-vars (opt.)  → Iterate on env vars (async → poll)
10. submit-env-vars        → Provide actual values (call even if list is empty — backend needs it to transition)
11. set-auth               → Generate bearer token for the server
12. generate-code          → Trigger code generation (async → poll)
13. deploy                 → Deploy to MCPHero runtime → returns server_url + bearer_token
```

**无论 `list-env_vars` 的返回结果是否为空（即 `[]`），都必须调用 `submit-env-vars`**。这是后台系统进入下一状态所必需的步骤。如果没有环境变量，只需不使用 `--var` 标志来调用该命令即可。**

---

## 状态机

`wizard_state` 中的 `setup_status` 字段可以显示当前的操作阶段：

```
gathering_requirements     → User is chatting about requirements
tools_generating           → LLM is generating tool suggestions (async, poll)
tools_selection            → Tools ready for review/selection
env_vars_generating        → LLM is generating env var suggestions (async, poll)
env_vars_setup             → Env vars ready for review/submission
auth_selection             → Ready for auth setup
code_generating            → LLM is generating code (async, poll)
code_gen                   → Code ready for review
deployment_selection       → Ready to deploy
ready                      → Server deployed and live
```

以 `_generating` 结尾的状态表示操作正在进行中——请持续轮询，直到状态发生变化。`processing_status` 字段是判断操作完成与否的可靠依据：`"idle"` 表示操作已完成，`"processing"` 表示操作正在进行中，`"error"` 表示存在错误，需要检查 `processing_error` 字段以获取具体错误信息。

---

## 完整的向导示例

在脚本化输出时，务必使用 `--json` 标志。如果不使用该标志，输出的信息将以人类可读的形式显示在标准错误输出（stderr）中，这可能会影响数据解析。

```bash
# 1. Create session
mcpheroctl wizard create-session --json
# → {"server_id": "abc-123-..."}
SERVER_ID="abc-123-..."

# 2. Describe requirements (iterate until is_ready: true)
mcpheroctl wizard conversation $SERVER_ID --json \
  -m "I have a PostgreSQL database with customers and orders tables. I need tools to find customers by name, fetch orders for a customer, and get last hour's orders."
# Repeat with follow-up messages until output shows: "is_ready": true

# 3. Start tool suggestion (async)
mcpheroctl wizard start $SERVER_ID --json
# → {"status": "processing"}

# Poll until processing is done (check processing_status, not setup_status)
until mcpheroctl wizard state $SERVER_ID --json 2>/dev/null | \
  python3 -c "import sys,json; exit(0 if json.load(sys.stdin).get('processing_status')=='idle' else 1)"; do
  sleep 3
done

# 4. Review tools (state should be "tools_selection")
mcpheroctl wizard list-tools $SERVER_ID --json

# 5. Refine if needed (async → poll again)
mcpheroctl wizard refine-tools $SERVER_ID --json \
  -f "Add error handling for missing customers. Rename get_customers_orders to get_orders_by_customer."

# 6. Submit selected tool IDs
mcpheroctl wizard submit-tools $SERVER_ID --json \
  --tool-id <tool-uuid-1> \
  --tool-id <tool-uuid-2> \
  --tool-id <tool-uuid-3>

# 7. Wait for env var suggestion (auto-triggered, state becomes "env_vars_setup")
until mcpheroctl wizard state $SERVER_ID --json 2>/dev/null | \
  python3 -c "import sys,json; exit(0 if json.load(sys.stdin).get('processing_status')=='idle' else 1)"; do
  sleep 3
done

# 8. Review env vars
mcpheroctl wizard list-env-vars $SERVER_ID --json

# 9. Submit env var values (format: VAR_UUID=VALUE)
# ALWAYS call this even if list-env-vars returned [] — backend needs it to transition.
# With env vars:
mcpheroctl wizard submit-env-vars $SERVER_ID --json \
  --var "<env-var-uuid-1>=localhost" \
  --var "<env-var-uuid-2>=5432"
# Without env vars (empty list):
mcpheroctl wizard submit-env-vars $SERVER_ID --json

# 10. Set authentication
mcpheroctl wizard set-auth $SERVER_ID --json
# → {"bearer_token": "..."}  ← SAVE THIS

# 11. Generate code (async → poll)
mcpheroctl wizard generate-code $SERVER_ID --json
until mcpheroctl wizard state $SERVER_ID --json 2>/dev/null | \
  python3 -c "import sys,json; exit(0 if json.load(sys.stdin).get('processing_status')=='idle' else 1)"; do
  sleep 3
done

# 12. Deploy
mcpheroctl wizard deploy $SERVER_ID --json
# → {"server_url": "/mcp/<server-id>/mcp", "bearer_token": "...", "step": "complete"}
```

**重要提示：** `deploy` 命令返回的服务器地址是一个相对路径（例如 `/mcp/<id>/mcp`），需要通过添加基础域名来获取完整的 URL：**

```
https://api.mcphero.app/mcp/<server-id>/mcp
```

---

## 服务器管理

```bash
mcpheroctl server list --json [CUSTOMER_ID]  # List all servers
mcpheroctl server get SERVER_ID --json       # Get server details + status
mcpheroctl server update SERVER_ID           # Update name/description
mcpheroctl server delete SERVER_ID --yes     # Delete (irreversible)
mcpheroctl server api-key SERVER_ID --json   # Retrieve bearer token
```

---

## 轮询机制

可靠的轮询方法是检查 `processing_status` 的值，而不是 `setup_status`：

```bash
until mcpheroctl wizard state $SERVER_ID --json 2>/dev/null | \
  python3 -c "import sys,json; exit(0 if json.load(sys.stdin).get('processing_status')=='idle' else 1)"; do
  sleep 3
done
```

---

## 将部署后的服务器连接到 MCP 客户端

在完成 `deploy` 操作后，需要构建服务器的完整 URL：

```
https://api.mcphero.app{server_url}
```

### Claude 桌面应用配置

```json
{
  "mcpServers": {
    "my-server": {
      "url": "https://api.mcphero.app/mcp/<server-id>/mcp",
      "headers": {
        "Authorization": "Bearer <bearer_token>"
      }
    }
  }
}
```

配置文件的位置：
- **macOS：** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows：** `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux：** `~/.config/claude/claude_desktop_config.json`

---

## 关键提示

- **免费 tier**：每台服务器最多只能使用 5 个工具。如果选择超过 5 个工具，`wizard_submit_tools` 命令会报错。
- **server_id 的重要性**：请保存 `create_session` 操作返回的 UUID。后续的所有调用都需要使用这个 UUID。
- **CLI 中的环境变量格式**：使用 `--var "UUID=VALUE"` 的格式来传递环境变量。这里的 UUID 是 `list-env-vars` 函数返回的环境变量的 **id**，而非其名称。
- **即使 `list-env_vars` 返回空数组（`[]`），也必须调用 `submit-env-vars`（即使不传递任何变量），以便后台系统能够进入下一状态。
- **无需重新部署即可更新代码**：使用 `wizard_regenerate_tool_code` 后，已部署的服务器的代码更改会立即生效（服务器会自动重新加载）。
- **始终使用 `--json` 标志**：在 CLI 命令中，`--json` 可以确保数据以结构化格式输出到标准输出（stdout）。如果不使用该标志，输出信息可能会影响数据解析。

---

## 错误代码

| 代码 | 含义 |
|------|---------|
| 0 | 操作成功 |
| 1 | 发生一般性错误 |
| 2 | 使用或参数错误 |
| 3 | 资源未找到 |
| 4 | 未通过身份验证 |
| 5 | 出现冲突 |

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
