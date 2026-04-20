---
name: claude-agent-sdk
description: Comprehensive guide for building production-ready agents with the Claude Agent SDK. Use when creating agents, designing tools, implementing subagents, managing sessions, integrating MCP servers, or understanding SDK-native features. Emphasizes documentation-first approach and using only SDK native capabilities. Use when this capability is needed.
metadata:
  author: neuro-synapse
---

# Claude Agent SDK Builder

**Documentation-First Agent Development**

## 🚨 THE CRITICAL PRINCIPLE

**USE ONLY THE SDK'S NATIVE CAPABILITIES**

```
❌ NEVER implement custom logic for functionality the SDK already handles
✅ ALWAYS check documentation BEFORE writing any code
✅ Every line of code must be justified by the official documentation
```

**Why this matters:**
- 70% of agents fail due to poor design
- Custom implementations of SDK features create technical debt
- The SDK evolves - your custom code becomes obsolete
- SDK features are battle-tested and optimized

**Documentation resources:**
- Main docs: https://docs.claude.com/en/api/agent-sdk/python
- Engineering guide: https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk

## Quick Start Checklist

Before writing ANY code:

- [ ] Read relevant SDK documentation sections
- [ ] Confirm SDK doesn't provide this feature natively
- [ ] Check Tool Design Framework if creating tools
- [ ] Review Subagent Patterns if using subagents
- [ ] Understand the agent feedback loop: gather context → take action → verify work

## The Agent Feedback Loop

Every agent operates in this cycle:

```
1. GATHER CONTEXT
   ↓
2. TAKE ACTION
   ↓
3. VERIFY WORK
   ↓
(repeat)
```

Design your agent's capabilities around this loop.

## Agent Creation Workflow

### Phase 1: Define Requirements

**Essential questions:**
- What user intention does this agent serve?
- What tools and capabilities does it need?
- Does it need session persistence? Subagents? MCP integration?
- What are security and performance requirements?

**Map requirements to SDK features:**

```python
# Before coding, identify needed features:
FEATURES_NEEDED = {
    "session_management": True,     # Resume conversations
    "streaming_mode": True,          # Responsive UX
    "permissions": True,             # Control tool access
    "mcp_integration": False,        # External data sources
    "subagents": "evaluate",         # Complex workflows
    "custom_tools": True,            # Domain-specific actions
    "cost_tracking": True,           # User billing
    "task_tracking": True            # Multi-step workflows
}

# Read documentation for each True/evaluate feature BEFORE coding
```

### Phase 2: Tool Design

**CRITICAL:** Read [references/tool-design-framework.md](references/tool-design-framework.md) for complete tool design guidance.

**Quick tool design checklist:**
- [ ] Tool models clear user intention (not API wrapper)
- [ ] Multi-step workflows consolidated in tool
- [ ] Parameters are natural language concepts
- [ ] Uses SDK's `@Tool.register` decorator
- [ ] Returns `ToolResult` objects
- [ ] Error handling uses `ToolError`

**SDK-native tool pattern:**

```python
from anthropic_sdk import Tool, ToolResult, ToolError

@Tool.register
def intention_based_name(
    natural_param: str,
    optional_param: str = "sensible_default"
) -> ToolResult:
    """
    Clear description of USER INTENTION this serves.
    NOT what the API does.
    """
    if not natural_param:
        raise ToolError(
            "Parameter required",
            recoverable=True,
            suggestion="Provide valid parameter"
        )
    
    # Tool consolidates ALL workflow steps
    result = complete_workflow(natural_param)
    
    return ToolResult(
        content=result,
        metadata={"confidence": 0.95},
        suggestions=["Try narrowing scope"]
    )
```

### Phase 3: Configure Agent

**Choose input mode:**

```python
from anthropic_sdk import Agent, InputMode

# Streaming for interactive agents (SDK-native)
agent = Agent(
    model="claude-sonnet-4-20250514",
    input_mode=InputMode.STREAMING,
    tools=[tool1, tool2]
)

# Single for batch/deterministic workflows (SDK-native)
agent = Agent(
    model="claude-sonnet-4-20250514",
    input_mode=InputMode.SINGLE,
    tools=[tool1, tool2]
)
```

**Configure system prompt (SDK-native methods):**

```python
# Method 1: CLAUDE.md file (recommended)
# Create CLAUDE.md in project root
# SDK automatically loads it

# Method 2: Append instructions
agent = Agent(
    model="claude-sonnet-4-20250514",
    system_prompt_append="Additional instructions...",
    tools=[...]
)

# Method 3: Output style presets
agent = Agent(
    model="claude-sonnet-4-20250514",
    output_style="concise",  # SDK presets
    tools=[...]
)
```

**Configure permissions (SDK-native):**

```python
from anthropic_sdk import PermissionMode

agent = Agent(
    model="claude-sonnet-4-20250514",
    tools=[tool1, tool2, tool3],
    permission_mode=PermissionMode.ASK,  # Ask before tool use
    # OR PermissionMode.AUTO  # Automatic
    # OR PermissionMode.RESTRICTED with allowed_tools
)
```

### Phase 4: Session Management (SDK-Native)

```python
# ❌ NEVER implement custom session storage
# ✅ SDK handles it natively

# Create session
session = agent.create_session(user_id="user_123")
session_id = session.id  # Track this ID

# Resume session
session = agent.resume_session(session_id)

# Fork session for branching
new_session = session.fork()

# SDK handles:
# - Session persistence
# - Conversation history
# - Context management
```

### Phase 5: MCP Integration (When Needed)

**Use MCP for:**
- External data sources
- Third-party API integrations
- Dynamic resource exposure

```python
from anthropic_sdk import MCPServer

agent = Agent(
    model="claude-sonnet-4-20250514",
    mcp_servers=[
        MCPServer(
            name="database",
            command="uvx",
            args=["mcp-server-sqlite", "--db-path", "data.db"]
        ),
        MCPServer(
            name="github",
            command="npx",
            args=["-y", "@modelcontextprotocol/server-github"],
            env={"GITHUB_TOKEN": os.getenv("GITHUB_TOKEN")}
        )
    ]
)
# SDK manages server lifecycle, auth, tool registration
```

### Phase 6: Subagent Architecture (When Needed)

**IMPORTANT:** Read [references/subagent-patterns.md](references/subagent-patterns.md) for complete patterns.

**Use subagents for:**
- Specialized agents for different domains
- Parallel processing of independent tasks
- Context isolation (sift through large data)
- Restricted tool access by task type

```python
from anthropic_sdk import Subagent

main_agent = Agent(
    model="claude-sonnet-4-20250514",
    tools=[coordination_tool]
)

# SDK-native subagent
research_subagent = Subagent(
    name="researcher",
    model="claude-sonnet-4-20250514",
    tools=[web_search, document_fetch],
    system_prompt="Research specialist instructions...",
    parent_agent=main_agent
)

# SDK handles orchestration, context isolation, result aggregation
```

### Phase 7: Built-in Features (SDK-Native)

**Slash commands:**
```python
# Built-in commands:
# /clear - Clear conversation history
# /compact - Compress context
# /session - Session management
# /usage - Token usage

# Custom commands
from anthropic_sdk import SlashCommand

@SlashCommand.register("summarize")
def summarize_conversation(agent: Agent, session: Session):
    return agent.summarize(session)
```

**Cost tracking:**
```python
result = agent.run("User message")

# SDK provides accurate metrics
print(f"Tokens: {result.usage.total_tokens}")
print(f"Cost: ${result.usage.estimated_cost}")

# Per-step metrics
for step in result.steps:
    print(f"{step.type}: {step.usage.total_tokens} tokens")
```

**Task tracking:**
```python
agent = Agent(
    model="claude-sonnet-4-20250514",
    enable_todos=True,  # SDK-native
    tools=[...]
)

result = agent.run("Plan and execute market analysis")

# SDK tracks tasks automatically
for task in result.todos:
    print(f"{task.description}: {task.status} ({task.progress}%)")
```

## Context Gathering Strategies

**Agentic search (recommended):**
- Use file system and bash for context gathering
- Claude uses grep, tail, etc. to load context intelligently
- More accurate and transparent than semantic search

**Semantic search (when needed):**
- Faster but less accurate
- Use only if agentic search is too slow
- Start with agentic, add semantic if necessary

**Subagents for context:**
- Spin up subagents to sift through large data
- Return only relevant excerpts to orchestrator
- Keeps main agent's context lean

**Context compaction (SDK-native):**
```python
agent = Agent(
    model="claude-sonnet-4-20250514",
    auto_compact=True,  # SDK automatically summarizes old messages
    tools=[...]
)
```

## Action Strategies

**Tools (primary actions):**
- Tools are prominent in context window
- Design as primary actions agent should take
- See references/tool-design-framework.md

**Bash & scripts:**
- General-purpose flexibility
- Let agent write and execute code
- Useful for data processing, API calls

**Code generation:**
- Precise, composable, reusable
- Consider: which tasks benefit from code?
- Example: Excel/PowerPoint generation via Python

**MCPs:**
- Standardized external integrations
- Handles auth and API calls automatically
- Growing ecosystem of pre-built servers

## Work Verification Strategies

**1. Define rules:**
```python
@Tool.register
def send_email(to: str, body: str) -> ToolResult:
    # Rule-based validation
    if not is_valid_email(to):
        raise ToolError("Invalid email address")
    
    if not has_sent_to_before(to):
        return ToolResult(
            content="Email sent",
            metadata={"warning": "First contact with this address"}
        )
```

**2. Visual feedback:**
- Screenshot rendered output
- Use Playwright MCP for automation
- Agent verifies visual correctness

**3. LLM as judge:**
- Another model evaluates output
- Heavy latency cost
- Use only when performance boost justifies cost

## Common Anti-Patterns

**❌ Custom session storage:**
```python
# WRONG
def save_session(data):
    with open(f"session_{id}.json", "w") as f:
        json.dump(data, f)

# CORRECT
session = agent.create_session(user_id="user_123")
```

**❌ Manual streaming:**
```python
# WRONG
def custom_stream(prompt):
    for chunk in api.stream(prompt):
        yield chunk

# CORRECT
agent = Agent(input_mode=InputMode.STREAMING)
```

**❌ Custom permissions:**
```python
# WRONG
if check_user_permission(user_id, tool_name):
    result = agent.run(message)

# CORRECT
agent = Agent(
    permission_mode=PermissionMode.RESTRICTED,
    allowed_tools=get_user_tools(user_id)
)
```

**❌ Custom token counting:**
```python
# WRONG
tokens = count_tokens_manually(text)

# CORRECT
result = agent.run(message)
tokens = result.usage.total_tokens
```

## SDK Feature Decision Tree

```
User Need?
├─ Session management? → create_session/resume_session
├─ Streaming? → InputMode.STREAMING
├─ Permissions? → PermissionMode + allowed_tools
├─ System prompt? → CLAUDE.md or system_prompt_append
├─ Cost tracking? → result.usage
├─ Task tracking? → enable_todos=True
├─ External APIs? → MCP servers
├─ Multi-agent? → Subagent feature
├─ Custom commands? → SlashCommand.register
├─ Tool creation? → Tool.register with ToolResult
└─ Something else? → Re-read docs, likely SDK handles it
```

## Pre-Production Checklist

**SDK usage:**
- [ ] All features use SDK-native implementations
- [ ] No custom code duplicating SDK functionality
- [ ] Latest SDK version installed

**Tool design:**
- [ ] Tools reviewed against Tool Design Framework
- [ ] Clear user intentions modeled
- [ ] SDK's Tool.register and ToolResult used

**Documentation:**
- [ ] All SDK features confirmed in official docs
- [ ] No assumptions about capabilities

**Performance & security:**
- [ ] Permission modes configured
- [ ] Cost tracking enabled
- [ ] Session management defined
- [ ] Error handling comprehensive

## Examples

**Customer support agent:**
```python
from anthropic_sdk import Agent, Tool, ToolResult, PermissionMode, InputMode

@Tool.register
def search_knowledge_base(query: str, category: str = None) -> ToolResult:
    results = kb_search(query, category)
    return ToolResult(
        content=results,
        metadata={"source": "kb", "confidence": 0.85}
    )

@Tool.register
def create_support_ticket(issue: str, priority: str = "medium") -> ToolResult:
    ticket = ticket_system.create(issue, priority)
    return ToolResult(
        content=f"Ticket #{ticket.id} created",
        metadata={"ticket_id": ticket.id}
    )

agent = Agent(
    model="claude-sonnet-4-20250514",
    input_mode=InputMode.STREAMING,
    permission_mode=PermissionMode.AUTO,
    tools=[search_knowledge_base, create_support_ticket],
    enable_todos=True,
    output_style="helpful"
)

def handle_chat(customer_id: str, message: str):
    session = agent.resume_session(f"customer_{customer_id}") or \
              agent.create_session(user_id=f"customer_{customer_id}")
    
    for chunk in agent.stream(message, session=session):
        yield chunk
```

## Additional Resources

- **Tool Design Framework:** [references/tool-design-framework.md](references/tool-design-framework.md)
- **Subagent Patterns:** [references/subagent-patterns.md](references/subagent-patterns.md)
- **SDK Capabilities:** [references/sdk-capabilities.md](references/sdk-capabilities.md)
- **Agent Initialization Script:** [scripts/init_agent.py](scripts/init_agent.py)
- **Tool Validation Script:** [scripts/validate_tool.py](scripts/validate_tool.py)

## Key Reminders

1. **Documentation first:** Read SDK docs before coding
2. **SDK-native:** Use built-in features, don't reinvent
3. **Tool design:** Follow framework for effective tools
4. **Test thoroughly:** Use real scenarios to validate
5. **Iterate:** Improve based on actual usage

**The SDK is your friend. Use it fully, trust it completely, read the documentation religiously.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neuro-synapse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
