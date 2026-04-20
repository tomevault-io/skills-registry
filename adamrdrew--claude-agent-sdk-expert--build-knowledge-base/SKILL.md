---
name: build-knowledge-base
description: Generates comprehensive SDK documentation by analyzing the claude-agent-sdk-python source code, examples, and docs. Creates a complete knowledge base for developers implementing with the SDK.
metadata:
  author: adamrdrew
---

# Build Knowledge Base Procedure

This is a procedural skill that generates comprehensive documentation for the Claude Agent SDK. Follow each step in order using TaskCreate to track progress. If any step fails, stop and report the error. Do not improvise or skip steps.

## Operating Principles

- Execute steps in strict sequence
- Use TaskCreate at the start to create tasks for each major step
- Use TaskUpdate to mark tasks in_progress when starting and completed when done
- If any step fails, halt execution and report the error
- Generate documentation that serves both human developers and AI assistants
- Focus on practical implementation guidance with code examples

## Procedure

### Step 1: Initialize Task Tracking

Use TaskCreate to create tasks for the following steps:
1. "Get SDK source code"
2. "Clear existing knowledge base"
3. "Analyze SDK structure and architecture"
4. "Document core API and classes"
5. "Document tools and tool creation"
6. "Document agents and configuration"
7. "Document examples and patterns"
8. "Generate index and cross-references"

### Step 2: Get SDK Source

Mark task 1 as in_progress. Execute the `/get-sdk-source` skill to clone or refresh the SDK repository. Mark task 1 as completed when done.

### Step 3: Clear Knowledge Base

Mark task 2 as in_progress. Execute the `/clear-knowledge-base` skill to remove existing documentation and reset the index. Mark task 2 as completed when done.

### Step 4: Analyze SDK Structure

Mark task 3 as in_progress. Thoroughly explore the SDK repository structure:

1. Read the SDK's README.md and any documentation in docs/
2. Examine the package structure in the src/ or main module directory
3. Identify all major components: core classes, tools, agents, utilities
4. Read pyproject.toml or setup.py to understand dependencies and entry points

Create `.claude-agent-sdk-expert/knowledge/library/architecture-overview.md` documenting:
- Overall SDK architecture and design philosophy
- Package structure and module organization
- Key dependencies and their purposes
- How the components fit together

Mark task 3 as completed.

### Step 5: Document Core API

Mark task 4 as in_progress. Analyze and document the core API:

1. Find and read all core class definitions (Agent, Tool, Message, etc.)
2. Document each class's purpose, constructor parameters, and methods
3. Include type hints and return types
4. Provide usage examples from your analysis

Create `.claude-agent-sdk-expert/knowledge/library/core-api.md` with comprehensive API documentation.

If the API is large, create additional files:
- `.claude-agent-sdk-expert/knowledge/library/messages-and-responses.md`
- `.claude-agent-sdk-expert/knowledge/library/configuration.md`

Mark task 4 as completed.

### Step 6: Document Tools

Mark task 5 as in_progress. Analyze the tools system:

1. Find tool-related classes and decorators
2. Understand how tools are defined, registered, and invoked
3. Document the tool creation patterns
4. Include examples of built-in tools and custom tool creation

Create `.claude-agent-sdk-expert/knowledge/library/tools-guide.md` covering:
- Tool definition syntax and decorators
- Parameter schemas and validation
- Tool execution flow
- Best practices for tool implementation
- Complete examples

Mark task 5 as completed.

### Step 7: Document Agents

Mark task 6 as in_progress. Analyze agent configuration and behavior:

1. Find agent class definitions and configuration options
2. Document agent lifecycle and execution model
3. Explain system prompts, context management, and conversation handling
4. Document any built-in agent types or patterns

Create `.claude-agent-sdk-expert/knowledge/library/agents-guide.md` covering:
- Agent instantiation and configuration
- System prompts and instructions
- Conversation and context management
- Agent execution patterns
- Multi-agent patterns if applicable

Mark task 6 as completed.

### Step 8: Document Examples

Mark task 7 as in_progress. Analyze all examples in the repository:

1. **List ALL example files**: Use `ls -la` on the examples/ directory to get a complete list of all .py files AND subdirectories
2. **Read EVERY example file**: Do not skip any example file. Read each one to understand its purpose
3. **Check for subdirectories**: If there are subdirectories (like plugins/), explore and document them too
4. Document what each example demonstrates
5. Extract reusable patterns and best practices

Create `.claude-agent-sdk-expert/knowledge/library/examples-catalog.md` with:
- **Complete list** of ALL example files (verify count matches the directory listing)
- Quick reference table listing every example file
- Key patterns demonstrated in each
- Code snippets with explanations
- When to use each pattern

Create `.claude-agent-sdk-expert/knowledge/library/best-practices.md` with:
- Recommended patterns from the examples
- Common pitfalls to avoid
- Performance considerations
- Testing strategies

**VERIFICATION STEP**: Before marking complete, run `ls .claude-agent-sdk-expert/claude-agent-sdk-python/examples/*.py | wc -l` to count example files and verify your examples-catalog.md includes ALL of them. If any are missing, add them before proceeding.

Mark task 7 as completed.

### Step 9: Generate Index

Mark task 8 as in_progress. Update the knowledge index:

1. Use the Read tool to get a list of all files created in `.claude-agent-sdk-expert/knowledge/library/`
2. For each file, write a concise but accurate description
3. Update `.claude-agent-sdk-expert/knowledge/index.md` with links to all documents

The index format should be:
```markdown
# Claude Agent SDK Knowledge Index

Consult this index to look up information related to developing for the Claude Agent SDK...

## Links to Documents

- [Architecture Overview](library/architecture-overview.md) - SDK design, package structure, and component relationships
- [Core API](library/core-api.md) - Main classes, methods, and type definitions
...
```

Mark task 8 as completed.

### Step 10: Final Report

Use TaskList to display all completed tasks. Report to the user:
- Number of documentation files created
- Summary of what was documented
- Any areas that may need manual review or enhancement

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
