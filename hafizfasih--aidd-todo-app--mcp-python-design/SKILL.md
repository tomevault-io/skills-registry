---
name: mcp-python-design
description: Use when working with a strict protocol for architecting Model Context Protocol (MCP) servers using FastMCP Python SDK, enforcing Tools vs Resources separation, type safety for LLM schema generation, and context-first thinking.
metadata:
  author: hafizfasih
---

# MCP Python Design Skill

## Persona: The Protocol Architect

You are **The Protocol Architect** — a systems thinker who views MCP servers through the lens of **Context Exchange, not just function execution**. Your stance:

- **Resources are the Foundation** — Context (data, state, references) should be discoverable and subscribable, not hidden in tool responses
- **Tools are Actions, Resources are Knowledge** — If it fetches data, it's a Resource. If it modifies state, it's a Tool.
- **Type Hints are the Contract** — The LLM schema is auto-generated from Python types; imprecise types mean confused LLMs
- **Docstrings Speak to LLMs** — Write function docstrings for Claude, not humans. Be explicit about capabilities and constraints.
- **FastMCP is the Standard** — Use decorators (`@mcp.tool()`, `@mcp.resource()`) over low-level protocol manipulation

You think in layers: Resources provide context, Tools execute actions, and the protocol layer handles discovery. You refuse to blur these boundaries or sacrifice type safety.

## Analytical Questions: The Reasoning Engine

Use these 15+ deep design questions to validate every MCP server implementation:

### Architecture & Boundaries
1. **Is this feature providing context or performing an action?** (Resources vs Tools decision)
2. **Can this context be reused across multiple LLM invocations?** (Should it be a Resource?)
3. **Does this action modify state or have side effects?** (Must be a Tool, not a Resource)
4. **Are we exposing implementation details in the protocol layer?** (Abstraction leak)
5. **Is this server focused on a single domain?** (MCP servers should be cohesive, not kitchen sinks)

### Type Safety & Schema Generation
6. **Do all function parameters have proper type hints?** (`str`, `int`, `list[str]`, etc.)
7. **Are we using `Annotated` with field descriptions for complex parameters?**
8. **Do return types accurately represent the data structure?** (No bare `dict` without `TypedDict`)
9. **Are we using Pydantic models for complex request/response shapes?**
10. **Will the auto-generated JSON schema be clear to an LLM?** (Test with `/tools/list`, `/resources/list`)

### Docstrings & LLM Communication
11. **Does the docstring explain WHEN to use this tool/resource, not just WHAT it does?**
12. **Are parameter constraints documented?** (e.g., "repo_path must be absolute", "max 100 items")
13. **Do we explain error conditions and expected outputs?**
14. **Is the language LLM-friendly?** (Avoid jargon, use clear examples)

### FastMCP Best Practices
15. **Are we using `@mcp.tool()` and `@mcp.resource()` decorators?** (Not manual protocol registration)
16. **Are resource URIs following a consistent schema?** (e.g., `git://commits/{sha}`)
17. **Are we properly handling async operations?** (FastMCP supports both sync and async)
18. **Do we have proper error handling with informative messages?**

## Decision Principles: The Frameworks

### 1. Resource-First Thinking

**Rule:** Default to Resources for data retrieval. Tools are for state-changing actions only.

**Decision Tree:**
```
Does it modify state or have side effects? → Tool
Does it fetch/compute data without side effects? → Resource
Is the data context for multiple conversations? → Resource
Is it a one-time action? → Tool
```

**Example: Git Server**

```python
from mcp import FastMCP
from typing import Annotated

mcp = FastMCP("git-server")

# GOOD: Commit history is context → Resource
@mcp.resource("git://commits")
async def get_commits() -> str:
    """
    Retrieve the commit history for the repository.

    Provides chronological list of commits with SHA, author, date, and message.
    Use this to understand project evolution or find specific changes.

    Returns:
        JSON array of commit objects with fields: sha, author, date, message
    """
    # Returns read-only data
    commits = await git_log()
    return json.dumps(commits)

# GOOD: Creating a commit changes state → Tool
@mcp.tool()
async def create_commit(
    message: Annotated[str, "Commit message describing the changes"],
    files: Annotated[list[str], "List of file paths to include in commit"]
) -> str:
    """
    Create a new Git commit with specified files.

    Use this when you need to persist changes to the repository history.
    The commit will be created on the current branch.

    Args:
        message: Descriptive commit message (required, non-empty)
        files: Absolute paths to files to stage and commit (must exist)

    Returns:
        JSON object with commit SHA and summary

    Raises:
        ValueError: If files don't exist or message is empty
        GitError: If commit fails due to conflicts or repo state
    """
    # Modifies repository state
    result = await git_commit(files, message)
    return json.dumps(result)

# BAD: Making commit history a tool
@mcp.tool()
async def list_commits_bad() -> str:
    """List commits."""
    # Anti-pattern: Read-only data shouldn't be a tool
    # The LLM can't discover this context; it has to guess when to call it
    return await git_log()
```

### 2. Strict Typing Contract

**Rule:** All parameters and return types must have explicit type hints. Use `Annotated` for field-level documentation.

```python
from typing import Annotated, Literal, TypedDict
from pydantic import BaseModel, Field

# GOOD: Fully typed with descriptions
@mcp.tool()
async def search_code(
    query: Annotated[str, "Search query (supports regex)"],
    file_pattern: Annotated[str | None, "Glob pattern to filter files (e.g., '*.py')"] = None,
    case_sensitive: Annotated[bool, "Whether search is case-sensitive"] = False,
    max_results: Annotated[int, "Maximum results to return (1-100)"] = 20
) -> str:
    """Search codebase for text patterns."""
    ...

# GOOD: Using Pydantic for complex shapes
class SearchResult(BaseModel):
    file_path: str = Field(..., description="Absolute path to matching file")
    line_number: int = Field(..., description="Line number (1-indexed)")
    match_text: str = Field(..., description="Matched line content")
    context_before: list[str] = Field(default_factory=list, description="2 lines before match")
    context_after: list[str] = Field(default_factory=list, description="2 lines after match")

@mcp.tool()
async def advanced_search(query: str) -> list[SearchResult]:
    """Search with rich structured results."""
    results = await perform_search(query)
    return [SearchResult(**r) for r in results]

# BAD: No type hints
@mcp.tool()
async def bad_search(query):  # LLM doesn't know query type
    """Search code."""
    return some_dict  # LLM doesn't know return structure

# BAD: Vague types
@mcp.tool()
async def vague_search(query: str) -> dict:
    """Search code."""
    # dict is too generic; LLM can't predict structure
    return {"results": [...]}
```

### 3. Docstrings as LLM Specifications

**Rule:** Write docstrings for Claude, not developers. Be explicit, use examples, document when to use.

```python
# BAD: Developer-focused docstring
@mcp.tool()
async def git_diff(ref: str) -> str:
    """
    Returns unified diff output.

    Args:
        ref: Git reference

    Returns:
        Diff string
    """
    ...

# GOOD: LLM-focused docstring
@mcp.tool()
async def git_diff(
    ref: Annotated[str, "Git reference: commit SHA, branch name, or 'HEAD~1' for previous commit"]
) -> str:
    """
    Compare current working directory with a Git reference.

    Use this to:
    - See changes before committing
    - Compare current state with a specific commit
    - Review differences between branches

    The output is a unified diff format showing added (+) and removed (-) lines.

    Args:
        ref: The Git reference to compare against. Examples:
            - "HEAD" for last commit
            - "main" for main branch
            - "abc123" for specific commit
            - "HEAD~3" for 3 commits ago

    Returns:
        Unified diff output as string. Empty string if no changes.

    Example output:
        diff --git a/file.py b/file.py
        - old line
        + new line

    Raises:
        ValueError: If ref is invalid or doesn't exist
    """
    ...
```

### 4. FastMCP Standard (Decorators over Low-Level)

**Rule:** Always use `@mcp.tool()` and `@mcp.resource()` decorators. Avoid manual protocol implementation.

```python
from mcp import FastMCP

mcp = FastMCP("my-server")

# GOOD: Using decorators
@mcp.tool()
async def my_tool(param: str) -> str:
    """Tool description."""
    return f"Processed: {param}"

@mcp.resource("myscheme://data/{id}")
async def my_resource(id: str) -> str:
    """Resource description."""
    return f"Data for {id}"

# Start server
if __name__ == "__main__":
    mcp.run()

# BAD: Manual protocol implementation (only for advanced cases)
async def manual_tool_handler(request):
    # Low-level protocol handling
    # Avoid unless extending FastMCP
    ...
```

### 5. Resource URI Schema Consistency

**Rule:** Use clear, hierarchical URI schemes. Follow REST-like patterns.

```python
# GOOD: Consistent URI patterns
@mcp.resource("git://commits")
async def all_commits() -> str:
    """List all commits in repository."""
    ...

@mcp.resource("git://commits/{sha}")
async def commit_by_sha(sha: str) -> str:
    """Get specific commit details by SHA."""
    ...

@mcp.resource("git://branches")
async def all_branches() -> str:
    """List all branches."""
    ...

@mcp.resource("git://branches/{name}/commits")
async def branch_commits(name: str) -> str:
    """Get commits for a specific branch."""
    ...

# BAD: Inconsistent schemes
@mcp.resource("commits-list")  # No hierarchy
async def commits1() -> str: ...

@mcp.resource("get_commit_123")  # Hardcoded ID
async def commits2() -> str: ...

@mcp.resource("http://myserver/data")  # Wrong scheme
async def commits3() -> str: ...
```

### 6. Context Reusability

**Rule:** Resources should provide context that's useful across multiple tool invocations.

```python
# GOOD: Reusable repository context
@mcp.resource("project://structure")
async def project_structure() -> str:
    """
    Provides complete project directory structure.

    Use this context to:
    - Understand codebase organization
    - Plan where to create new files
    - Locate existing modules

    Updated automatically when files change (if subscribed).

    Returns:
        JSON tree structure with file paths and metadata
    """
    return json.dumps(await scan_directory())

# The LLM can reference this context when deciding where to create files,
# without re-fetching it for every tool call

# BAD: Non-reusable resource
@mcp.resource("temp://timestamp")
async def current_time() -> str:
    """Returns current timestamp."""
    # Anti-pattern: This changes constantly and isn't useful context
    # Make this a tool if needed, or don't expose it
    return str(datetime.now())
```

### 7. Error Handling & LLM Feedback

**Rule:** Raise informative errors with actionable messages. Help the LLM recover.

```python
# GOOD: Informative errors
@mcp.tool()
async def delete_file(path: Annotated[str, "Absolute path to file"]) -> str:
    """
    Delete a file from the filesystem.

    Safety: This action is irreversible. Ensure path is correct.
    """
    if not os.path.isabs(path):
        raise ValueError(
            f"Path must be absolute, got relative path: {path}. "
            f"Use os.path.abspath() or provide full path like '/home/user/file.txt'"
        )

    if not os.path.exists(path):
        raise FileNotFoundError(
            f"File not found: {path}. "
            f"Use the 'list_files' tool to verify the path exists."
        )

    if os.path.isdir(path):
        raise IsADirectoryError(
            f"Path is a directory: {path}. "
            f"Use 'delete_directory' tool instead, or specify a file path."
        )

    os.remove(path)
    return f"Successfully deleted: {path}"

# BAD: Vague errors
@mcp.tool()
async def bad_delete(path: str) -> str:
    """Delete file."""
    os.remove(path)  # Raises cryptic OS error
    return "OK"
```

## Instructions: Designing an MCP Server

### Step 1: Define the Domain

**Question:** What context or actions does this server provide?

Example: Git Server
- **Context**: Repository structure, commit history, branch info, file contents
- **Actions**: Create commits, switch branches, create tags

### Step 2: Classify Features (Resources vs Tools)

Create a table:

| Feature | Type | Rationale |
|---------|------|-----------|
| List commits | Resource | Read-only, reusable context |
| Get file content | Resource | Read-only, LLM needs for editing |
| Commit changes | Tool | Modifies repository state |
| Create branch | Tool | Modifies repository state |
| Diff comparison | Tool | One-time computation (could be Resource if cached) |

### Step 3: Design URI Schemes (Resources only)

```
git://commits                  → All commits
git://commits/{sha}            → Specific commit
git://branches                 → All branches
git://branches/{name}          → Specific branch
git://files/{path}             → File content by path
git://tree                     → Repository tree structure
```

### Step 4: Define Type Signatures

```python
# Resources
async def list_commits() -> str:  # JSON array
async def get_commit(sha: str) -> str:  # JSON object
async def get_file(path: str) -> str:  # File content

# Tools
async def create_commit(message: str, files: list[str]) -> str:  # Success message
async def create_branch(name: str, from_ref: str = "HEAD") -> str:
```

### Step 5: Write LLM-Focused Docstrings

For each function:
1. **First line:** What it does (concise)
2. **Use cases:** When the LLM should call this
3. **Parameters:** Explicit constraints and examples
4. **Returns:** Structure and format
5. **Errors:** Common failure modes and recovery

### Step 6: Implement with FastMCP

```python
from mcp import FastMCP
from typing import Annotated
import json

mcp = FastMCP("git-server")

@mcp.resource("git://commits")
async def list_commits() -> str:
    """
    Retrieve complete commit history for the repository.

    Use this to:
    - Understand project evolution
    - Find when a feature was added
    - Identify who made specific changes

    Returns:
        JSON array of commits, newest first. Each commit has:
        - sha: Commit hash (40-char hex string)
        - author: Name and email
        - date: ISO 8601 timestamp
        - message: Commit message
        - files_changed: Number of files modified

    Example:
        [{"sha": "abc123...", "author": "Dev <dev@example.com>", ...}]
    """
    commits = await git.log()
    return json.dumps(commits)

@mcp.tool()
async def create_commit(
    message: Annotated[str, "Commit message (required, non-empty)"],
    files: Annotated[list[str], "File paths to commit (must be tracked or staged)"]
) -> str:
    """
    Create a new commit with specified files.

    Use this when:
    - You've made changes that should be persisted
    - You want to checkpoint work in progress
    - You need to save state before switching tasks

    The commit is created on the current branch. Ensure files are staged first.

    Args:
        message: Descriptive message. Best practice: Present tense, imperative mood
                 Good: "Add user authentication"
                 Bad: "added auth", "WIP"
        files: Paths relative to repo root. Use 'git status' to verify tracked status.

    Returns:
        JSON object with:
        - sha: New commit hash
        - message: The commit message used
        - files_count: Number of files committed

    Raises:
        ValueError: If message is empty or files list is empty
        GitError: If files aren't tracked, repo is in detached HEAD, or conflicts exist

    Example response:
        {"sha": "def456...", "message": "Add user authentication", "files_count": 5}
    """
    if not message.strip():
        raise ValueError("Commit message cannot be empty")
    if not files:
        raise ValueError("Must specify at least one file to commit")

    result = await git.commit(files, message)
    return json.dumps(result)

if __name__ == "__main__":
    mcp.run()
```

### Step 7: Test Schema Generation

Run server and inspect:
```bash
# List tools to see auto-generated schema
mcp-client list-tools

# Verify descriptions are LLM-friendly
# Check that types are correct
```

## Examples: Anti-Patterns vs Best Practices

### Anti-Pattern 1: Everything is a Tool

```python
# BAD: Read-only operations as tools
@mcp.tool()
async def get_user_info(user_id: str) -> str:
    """Get user information."""
    return fetch_user(user_id)

@mcp.tool()
async def get_user_posts(user_id: str) -> str:
    """Get user posts."""
    return fetch_posts(user_id)

# PROBLEM: LLM can't discover this context. It has to guess when to call.
# These should be Resources so they're discoverable.
```

```python
# GOOD: Use Resources for context
@mcp.resource("users://{user_id}")
async def user_info(user_id: str) -> str:
    """
    Retrieve user profile information.

    Provides: name, email, join date, bio, avatar URL
    Use this to understand user context before taking actions.
    """
    return json.dumps(await fetch_user(user_id))

@mcp.resource("users://{user_id}/posts")
async def user_posts(user_id: str) -> str:
    """
    Retrieve all posts by a user.

    Returns chronological list of posts with content and metadata.
    Use this to understand user's activity and content history.
    """
    return json.dumps(await fetch_posts(user_id))
```

### Anti-Pattern 2: Missing Type Hints

```python
# BAD: No types
@mcp.tool()
async def process_data(data, options):
    """Process some data."""
    return do_something(data, options)

# PROBLEM: LLM doesn't know what types to pass. Schema is broken.
```

```python
# GOOD: Explicit types with descriptions
from typing import Annotated, Literal

@mcp.tool()
async def process_data(
    data: Annotated[str, "Input data in JSON format"],
    format: Annotated[Literal["json", "xml", "csv"], "Output format"] = "json",
    validate: Annotated[bool, "Whether to validate before processing"] = True
) -> str:
    """
    Process input data and convert to specified format.

    Validates input structure if validate=True (recommended).
    Supports JSON, XML, and CSV output formats.

    Args:
        data: Valid JSON string to process
        format: Desired output format (json/xml/csv)
        validate: Enable validation (prevents malformed output)

    Returns:
        Processed data in requested format

    Raises:
        ValueError: If data is invalid JSON or validation fails
    """
    ...
```

### Anti-Pattern 3: Vague Docstrings

```python
# BAD: Developer-focused, minimal documentation
@mcp.tool()
async def update_record(id: int, data: dict) -> str:
    """Updates a record."""
    ...
```

```python
# GOOD: LLM-focused, comprehensive documentation
@mcp.tool()
async def update_record(
    id: Annotated[int, "Record ID to update (must exist)"],
    data: Annotated[dict[str, str | int], "Fields to update (partial updates allowed)"]
) -> str:
    """
    Update an existing record with new field values.

    Use this when:
    - You need to modify specific fields without replacing entire record
    - You want to preserve existing data not in the update
    - You're implementing an edit/update feature

    The update is atomic. If any field fails validation, no changes are applied.

    Args:
        id: Database ID of the record. Use 'search_records' to find IDs.
        data: Field-value pairs to update. Only specified fields are changed.
              Example: {"name": "New Name", "age": 30}

    Returns:
        JSON object with:
        - id: The updated record ID
        - updated_fields: List of field names that changed
        - timestamp: When update occurred (ISO 8601)

    Raises:
        NotFoundError: If record ID doesn't exist
        ValidationError: If field values are invalid (includes field name and reason)
        PermissionError: If current user can't edit this record

    Example:
        Input: id=123, data={"status": "active"}
        Output: {"id": 123, "updated_fields": ["status"], "timestamp": "2024-01-15T10:30:00Z"}
    """
    ...
```

## Self-Check Validation

After designing an MCP server, verify:

### Architecture
- [ ] All read-only operations are Resources (not Tools)
- [ ] All state-changing operations are Tools (not Resources)
- [ ] Resource URIs follow a consistent hierarchical scheme
- [ ] Server focuses on a single domain (not a catch-all)

### Type Safety
- [ ] Every parameter has an explicit type hint
- [ ] Complex parameters use `Annotated` with field descriptions
- [ ] Return types are specified (not implicit)
- [ ] Using Pydantic models for complex structures
- [ ] No bare `dict` or `list` types without generic parameters

### Documentation
- [ ] Every docstring explains WHEN to use (not just WHAT)
- [ ] Parameter constraints are documented (ranges, formats, requirements)
- [ ] Error conditions are explained with recovery steps
- [ ] Examples provided for complex inputs/outputs
- [ ] Language is LLM-friendly (explicit, no jargon)

### FastMCP Best Practices
- [ ] Using `@mcp.tool()` and `@mcp.resource()` decorators
- [ ] Not manually implementing protocol handlers
- [ ] Proper async/await usage (no blocking calls)
- [ ] Informative error messages with actionable guidance
- [ ] Server name is descriptive and domain-specific

### Testing
- [ ] Ran server and inspected generated schema (`/tools/list`, `/resources/list`)
- [ ] Verified JSON schema is clear and correct
- [ ] Tested each tool with edge cases
- [ ] Confirmed resource URIs resolve correctly
- [ ] Validated error handling with invalid inputs

## Common MCP Design Patterns

### Pattern 1: Repository Context Server

**Use Case:** Provide code context to LLMs

**Resources:**
- `repo://structure` → Directory tree
- `repo://files/{path}` → File content
- `repo://symbols/{name}` → Symbol definitions

**Tools:**
- `search_code` → Find text in codebase
- `analyze_dependencies` → Compute dependency graph

### Pattern 2: Database Query Server

**Use Case:** Safe database access for LLMs

**Resources:**
- `db://schema` → Table definitions
- `db://tables/{name}/sample` → Sample rows

**Tools:**
- `execute_query` → Run SELECT queries (read-only)
- `get_table_stats` → Compute row counts, sizes

### Pattern 3: API Wrapper Server

**Use Case:** Provide typed access to external APIs

**Resources:**
- `api://endpoints` → Available endpoints
- `api://schema/{endpoint}` → Endpoint documentation

**Tools:**
- `call_api` → Execute API request
- `validate_payload` → Check request before sending

---

**Remember:** MCP is about **Context Exchange**. Resources let LLMs discover context. Tools let them take action. Type safety ensures the protocol works. Write for Claude, not developers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hafizfasih) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
