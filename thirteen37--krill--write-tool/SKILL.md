---
name: write-tool
description: This skill should be used when the user asks to "create a tool", "add a tool", "implement a tool", "write a new tool", "tool structure", "tool docstrings", "use claude_code_agent", or needs guidance on Krill tool development and self-modification. Use when this capability is needed.
metadata:
  author: thirteen37
---

# Writing Krill Tools

This skill guides you through creating new tools for Krill by leveraging the `claude_code_agent` tool for safe self-modification.

## When to Create a Tool vs Skill

**Create a TOOL when:**
- You need new functionality that doesn't exist
- The capability requires code execution
- You want to extend Krill's core abilities
- The feature needs access to system resources

**Create a SKILL when:**
- You're combining existing tools in a workflow
- You need domain-specific guidance
- The task follows a reusable pattern
- No code changes are required

## Tool Development Workflow

### Step 1: Enable claude_code_agent

First, ensure the self-modification tool is enabled in your config:

```toml
# ~/.krill/config.toml
[tools]
enabled = [
    # ... other tools ...
    "claude_code_agent"
]
```

### Step 2: Describe WHAT, Not HOW

Write a clear task description focusing on **what** to implement, not **how**:

**Good task descriptions:**
```
Add a tool to calculate moon phases
Add a tool to fetch GitHub issues for a repository
Add a tool to convert markdown to PDF
```

**Bad task descriptions (too prescriptive):**
```
Use astronomia library to implement lunar phase calculations
Create a GitHub API client class with rate limiting and retry logic
Install wkhtmltopdf and wrap it with subprocess calls
```

Let Claude Code figure out the implementation details.

### Step 3: Specify Scope

Always specify scope to prevent unintended modifications:

```python
scope = [
    "krill/tools/moon.py",           # The tool implementation
    "tests/test_tools_moon.py",      # Tests for the tool
    "docs/tools.md",                 # Tool documentation
]
```

**Scope patterns:**
- `"krill/tools/"` - All tool files
- `"krill/tools/foo.py"` - Specific file
- `"tests/test_tools_*.py"` - Test files pattern

### Step 4: Invoke claude_code_agent

Use the tool to spawn Claude Code for implementation:

```
I need a tool to calculate moon phases. Can you add that using claude_code_agent?
```

Or more explicitly:

```
Use claude_code_agent to add a tool for moon phase calculations.
Scope: krill/tools/moon.py and tests/test_tools_moon.py
```

### Step 5: Review Results

Claude Code will:
1. Create an isolated git worktree
2. Implement the tool following Krill conventions
3. Write comprehensive tests
4. Run all validation checks
5. Return a summary with:
   - Validation results
   - Change summary
   - Worktree path
   - Next steps

### Step 6: Inspect and Merge

Review the changes in the worktree:

```bash
cd /path/to/worktree
git diff main

# Run tests manually if desired
uv run pytest tests/test_tools_moon.py -v

# Check the implementation
cat krill/tools/moon.py
```

If satisfied, merge to main:

```bash
cd /path/to/main/worktree
git merge branch-name
```

Or if you want to iterate:

```bash
cd /path/to/worktree
# Make changes
# Run tests
# Commit
```

## Tool Docstring Requirements (CRITICAL)

Tool docstrings serve as **LLM-facing metadata**. The docstring determines when and how Krill uses the tool.

### Format (REQUIRED)

The `@agent.tool` decorated function must have a comprehensive docstring:

```python
@agent.tool
async def tool_name(
    ctx: RunContext,  # type: ignore[type-arg]
    arg1: str,
    arg2: int = 10,
) -> str:
    """
    One-line summary of what the tool does.

    Use this when the user asks to... [specific scenarios]

    Args:
        arg1: Description of arg1
        arg2: Description of arg2 (default: 10)

    Returns:
        Description of what the tool returns

    Example:
        result = await tool_name("value", 42)
    """
```

### Docstring Sections (REQUIRED)

1. **Summary line** - One line describing the tool
2. **Use cases** - "Use this when the user asks to..."
3. **Args** - Description of each parameter with types/defaults
4. **Returns** - What the tool returns
5. **Example** - Code example showing usage

### Writing Effective Docstrings

✅ **Good:**
```python
"""
Execute a shell command with timeout and output capture.

Use this when the user asks to run commands, execute scripts,
check system status, or interact with the shell.

Args:
    command: Shell command to execute
    timeout: Maximum execution time in seconds (default: 30)

Returns:
    Command output as string, or error message if failed

Example:
    output = await shell("git status")
"""
```

**Why good:** Clear summary, specific use cases, complete parameter docs, return description, example.

❌ **Bad:**
```python
"""Runs a command."""  # No use cases, no args, no example
```

**Why bad:** Too terse, LLM doesn't know when to use it or how.

## Tool Implementation Requirements

### File Structure

New tools follow this structure:

```python
"""Tool description here."""

import structlog

from krill.tools.registry import tool

log = structlog.get_logger(__name__)


async def _run_tool_name(
    arg1: str,
    arg2: int,
) -> str:
    """
    Internal implementation function.

    Args:
        arg1: Description
        arg2: Description

    Returns:
        Tool result as string
    """
    # Implementation here
    pass


def register(agent, **kwargs) -> None:
    """Register the tool on an agent."""

    @tool
    async def tool_name(
        arg1: str,
        arg2: int = 10,
    ) -> str:
        """Tool description for the LLM.

        Use this when the user asks to...

        Args:
            arg1: Description
            arg2: Description (default: 10)

        Returns:
            Description of what the tool returns
        """
        return await _run_tool_name(
            arg1=arg1,
            arg2=arg2,
        )

    agent.register_tool(tool_name)
```

### Sandbox Integration

All tools must respect the sandbox approval system:

```python
from krill.sandbox import Sandbox, ToolDecision

# In your tool implementation:
if safety:
    decision = safety.check_command(command)
    log.info("tool.sandbox_check", decision=decision.value)

    if decision == ToolDecision.DENY:
        return "ERROR: Operation blocked by sandbox policy"

    if decision == ToolDecision.ASK and approval_fn:
        approved = await approval_fn(
            call_id=f"tool_{uuid.uuid4().hex[:8]}",
            tool_name="tool_name",
            args={"arg1": arg1},
        )
        if not approved:
            log.info("tool.denied")
            return "User denied operation"
```

### Testing Requirements

Every tool MUST include comprehensive tests:

```python
"""Tests for the tool_name tool."""

import pytest
from unittest.mock import AsyncMock, Mock, patch

from krill.tools.tool_name import _run_tool_name


class TestToolName:
    """Test the tool_name functionality."""

    @pytest.mark.asyncio
    async def test_basic_operation(self):
        """Test basic tool operation."""
        result = await _run_tool_name(
            arg1="test",
            arg2=42,
            safety=None,
            approval_fn=None,
        )

        assert "expected" in result

    @pytest.mark.asyncio
    async def test_sandbox_deny(self):
        """Test sandbox denial."""
        from krill.sandbox import Sandbox, ToolDecision

        safety = Mock(spec=Sandbox)
        safety.check_command.return_value = ToolDecision.DENY

        result = await _run_tool_name(
            arg1="test",
            arg2=42,
            safety=safety,
            approval_fn=None,
        )

        assert "blocked" in result.lower()

    @pytest.mark.asyncio
    async def test_approval_flow(self):
        """Test approval request and approval."""
        from krill.sandbox import Sandbox, ToolDecision

        safety = Mock(spec=Sandbox)
        safety.check_command.return_value = ToolDecision.ASK
        approval_fn = AsyncMock(return_value=True)

        result = await _run_tool_name(
            arg1="test",
            arg2=42,
            safety=safety,
            approval_fn=approval_fn,
        )

        # Verify approval was requested
        approval_fn.assert_called_once()
        assert "expected" in result
```

### Documentation Requirements

Update `docs/tools.md` with:

1. **Tool table entry:**
```markdown
| Tool | Module | Purpose |
|---|---|---|
| `tool_name` | `tools/tool_name.py` | Description of what it does |
```

2. **Detailed section** (if complex):
```markdown
## Tool Name

Description of the tool.

**Arguments:**
- `arg1` - Description
- `arg2` - Description (optional)

**Returns:** Description

**Example:**
```python
result = await tool_name("value", 42)
```

**Security considerations:**
- What sandbox checks apply
- What requires approval
```

## Registration Pattern

### Default Tools

Add to `krill/agent.py` in the default tool list:

```python
default_tools = [
    "memory_read",
    "memory_write",
    # ... other tools ...
    "tool_name",  # Your new tool
]
```

### Opt-In Tools

For specialized tools, register conditionally:

```python
if "tool_name" in enabled:
    from krill.tools.tool_name import register as register_tool_name
    register_tool_name(agent, safety=sandbox, approval_fn=approval_fn)
```

## Best Practices

### Code Quality

- **Type hints everywhere** - All function signatures need types
- **Async by default** - Use `async def` for tool functions
- **Error handling** - Catch exceptions, return error messages
- **Logging** - Use structlog for all logging
- **Clean docstrings** - Clear descriptions for the LLM

### Security

- **Never hardcode secrets** - Use credential management
- **Validate inputs** - Don't trust user input blindly
- **Respect sandbox** - Always check approval before dangerous ops
- **Fail safely** - Default to denial, not allowance

### Testing

- **Test all code paths** - Happy path, errors, edge cases
- **Test sandbox integration** - ALLOW, DENY, ASK scenarios
- **Test error conditions** - Network failures, invalid input
- **Regression tests** - Every bug fix needs a test

### Documentation

- **Update docs in same commit** - Don't leave stale docs
- **Clear examples** - Show real usage
- **Document limitations** - What the tool can't do
- **Security notes** - Approval requirements

## Common Patterns

### API Client Tools

```python
async def _run_api_tool(
    query: str,
    safety: Sandbox | None,
    approval_fn: "ApprovalFn | None",
) -> str:
    """Fetch from external API."""

    # Check if we need approval for network access
    if safety:
        decision = ToolDecision.ASK  # External APIs always ask
        if decision == ToolDecision.ASK and approval_fn:
            approved = await approval_fn(...)
            if not approved:
                return "User denied API access"

    # Make API request
    try:
        response = await client.get(f"/api/{query}")
        return response.json()
    except Exception as exc:
        log.exception("api_tool.error")
        return f"ERROR: API request failed ({type(exc).__name__})"
```

### File Processing Tools

```python
async def _run_file_tool(
    path: str,
    safety: Sandbox | None,
    approval_fn: "ApprovalFn | None",
) -> str:
    """Process a file."""

    # Check file read permission
    if safety:
        decision = safety.check_file_read(path)
        if decision == ToolDecision.DENY:
            return "ERROR: File read blocked"
        if decision == ToolDecision.ASK and approval_fn:
            approved = await approval_fn(...)
            if not approved:
                return "User denied file access"

    # Read and process file
    try:
        content = Path(path).read_text()
        result = process(content)
        return result
    except FileNotFoundError:
        return f"ERROR: File not found: {path}"
    except Exception as exc:
        log.exception("file_tool.error")
        return f"ERROR: Processing failed ({type(exc).__name__})"
```

### Shell Command Tools

```python
async def _run_shell_tool(
    command: str,
    safety: Sandbox | None,
    approval_fn: "ApprovalFn | None",
) -> str:
    """Execute shell command."""

    # Check command permission
    if safety:
        decision = safety.check_command(command)
        if decision == ToolDecision.DENY:
            return "ERROR: Command blocked"
        if decision == ToolDecision.ASK and approval_fn:
            approved = await approval_fn(...)
            if not approved:
                return "User denied command"

    # Execute command
    try:
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True,
            timeout=30,
        )
        return result.stdout
    except subprocess.TimeoutExpired:
        return "ERROR: Command timed out"
    except Exception as exc:
        log.exception("shell_tool.error")
        return f"ERROR: Execution failed ({type(exc).__name__})"
```

## Troubleshooting

**claude_code_agent not found?**
- Check `[tools] enabled` in config.toml
- Verify tool is registered in agent.py
- Restart Krill server

**Validation failing?**
- Check convention violations in worktree
- Run `uv run krill check-conventions`
- Run `uv run krill validate-merge .`
- Check test output in worktree

**Tests not passing?**
- Run `uv run pytest` in worktree
- Check for import errors
- Verify sandbox mocks are correct
- Check async test markers

**Merge conflicts?**
- Rebase worktree branch on main
- Resolve conflicts in worktree
- Re-run validation

## Examples

### Simple Tool

Task: "Add a tool to get the current date"

```
Use claude_code_agent to add a tool that returns the current date.
Scope: krill/tools/date.py and tests/test_tools_date.py
```

### API Integration Tool

Task: "Add a tool to fetch weather data"

```
Use claude_code_agent to add a tool that fetches weather data for a given city.
Scope: krill/tools/weather.py and tests/test_tools_weather.py

The tool should use a weather API and require approval before making requests.
```

### File Processing Tool

Task: "Add a tool to count lines in a file"

```
Use claude_code_agent to add a tool that counts lines in a file.
Scope: krill/tools/linecount.py and tests/test_tools_linecount.py

The tool should respect sandbox file read permissions.
```

## Common Mistakes

### Mistake 1: Weak Tool Docstring

❌ **Bad:**
```python
"""File reader."""
```

**Why bad:** No use cases, no parameters documented, LLM doesn't know when to use it.

✅ **Good:**
```python
"""
Read file contents from the workspace.

Use this when the user asks to read a file, view file contents,
or check what's in a file.

Args:
    path: File path relative to workspace

Returns:
    File contents as string, or error message if not found

Example:
    content = await file_read("config.toml")
"""
```

**Why good:** Clear use cases, documented args, complete example.

### Mistake 2: Too Prescriptive in Task Description

❌ **Bad:**
```
Use astronomia library to implement lunar phase calculations
with rate limiting and retry logic
```

**Why bad:** Specifies HOW to implement, constrains claude_code_agent.

✅ **Good:**
```
Add a tool to calculate moon phases
```

**Why good:** Describes WHAT to build, lets claude_code_agent choose implementation.

### Mistake 3: No Sandbox Integration

❌ **Bad:**
```python
# Tool executes without checking permissions
result = subprocess.run(command, shell=True)
```

**Why bad:** Bypasses security, no user approval.

✅ **Good:**
```python
if safety:
    decision = safety.check_command(command)
    if decision == ToolDecision.DENY:
        return "ERROR: Operation blocked"
    if decision == ToolDecision.ASK and approval_fn:
        approved = await approval_fn(...)
        if not approved:
            return "User denied operation"
```

**Why good:** Respects sandbox, requests approval when needed.

### Mistake 4: Missing Tests

❌ **Bad:**
```
# Tool implemented but no tests
```

**Why bad:** No regression protection, can't verify correctness.

✅ **Good:**
```python
@pytest.mark.asyncio
async def test_basic_operation():
    result = await _run_tool_name("test", 42, None, None)
    assert "expected" in result

@pytest.mark.asyncio
async def test_sandbox_deny():
    safety = Mock(spec=Sandbox)
    safety.check_command.return_value = ToolDecision.DENY
    result = await _run_tool_name("test", 42, safety, None)
    assert "blocked" in result.lower()
```

**Why good:** Tests happy path, sandbox integration, error cases.

## Validation Checklist

Before finalizing a tool:

**Docstring Quality (CRITICAL):**
- [ ] Has one-line summary
- [ ] Includes "Use this when..." section with specific scenarios
- [ ] All Args documented with types and defaults
- [ ] Returns section describes output
- [ ] Example shows actual usage
- [ ] Clear enough for LLM to know when to invoke

**Implementation Quality:**
- [ ] Type hints on all function signatures
- [ ] Async by default (`async def`)
- [ ] Error handling with try/except
- [ ] Structured logging via structlog
- [ ] Sandbox integration if dangerous operations

**Testing Quality:**
- [ ] Tests for happy path
- [ ] Tests for error conditions
- [ ] Tests for sandbox ALLOW/DENY/ASK
- [ ] Tests for approval flow
- [ ] All tests passing

**Documentation:**
- [ ] Entry in docs/tools.md table
- [ ] Detailed section if complex
- [ ] Security considerations documented
- [ ] Registration in agent.py

## See Also

- `write-skill` - Guide for creating Krill skills
- `write-agent` - Guide for creating Krill agents
- `docs/tools.md` - Tool documentation
- `docs/self-modification.md` - Architecture of claude_code_agent

---
> Source: [thirteen37/Krill](https://github.com/thirteen37/Krill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
