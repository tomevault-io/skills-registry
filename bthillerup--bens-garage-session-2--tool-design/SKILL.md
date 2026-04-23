---
name: tool-design
description: This skill should be used when the user asks to "design agent tools", "create tool descriptions", "reduce tool complexity", "implement MCP tools", or mentions tool consolidation, architectural reduction, or agent-tool interfaces. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Tool Design for Agents

Tools are contracts between deterministic systems and non-deterministic agents. Unlike traditional APIs designed for developers, tool APIs must be designed for language models that reason about intent and infer parameter values.

## When to Activate

Activate this skill when:
- Creating new tools for agent systems
- Debugging tool-related failures or misuse
- Optimizing existing tool sets for better agent performance
- Designing tool APIs from scratch

## Core Principles

### The Consolidation Principle
If a human engineer cannot definitively say which tool should be used in a given situation, an agent cannot be expected to do better.

**Prefer single comprehensive tools over multiple narrow tools**.

### Tool Description as Prompt
Tool descriptions are loaded into agent context and collectively steer behavior. They're not just documentation—they're prompt engineering.

### Architectural Reduction
Sometimes removing tools improves performance. Vercel's d0 agent achieved 100% success rate (up from 80%) by reducing from 17 tools to 2 primitives: bash and SQL.

## Tool Description Engineering

Effective descriptions answer four questions:

1. **What does it do?** Clear, specific description
2. **When should it be used?** Specific triggers and contexts
3. **What inputs does it accept?** Parameter descriptions with types, constraints, defaults
4. **What does it return?** Output format and examples

### Well-Designed Tool Example

```python
def get_customer(customer_id: str, format: str = "concise"):
    """
    Retrieve customer information by ID.
    
    Use when:
    - User asks about specific customer details
    - Need customer context for decision-making
    
    Args:
        customer_id: Format "CUST-######" (e.g., "CUST-000001")
        format: "concise" for key fields, "detailed" for complete record
    
    Returns:
        Customer object with requested fields
    
    Errors:
        NOT_FOUND: Customer ID not found
        INVALID_FORMAT: ID must match CUST-###### pattern
    """
```

### Poor Tool Design (Anti-Pattern)

```python
def search(query):
    """Search the database."""
    pass
```

**Problems**: Vague name, missing parameters, no return description, no usage context.

## Response Format Optimization

Tool response size significantly impacts context usage. Implement format options:

- **Concise**: Essential fields only (confirmation, basic info)
- **Detailed**: Complete objects with all fields

## Error Message Design

Design error messages for agent recovery:
- What went wrong
- How to correct it
- Retry guidance for retryable errors
- Correct format for input errors

## MCP Tool Naming

When using MCP, always use fully qualified names:

```python
# Correct
"Use the BigQuery:bigquery_schema tool to retrieve schemas."

# Incorrect - may fail with multiple servers
"Use the bigquery_schema tool..."
```

## Tool Collection Design

Research shows tool description overlap causes confusion. Guideline: **10-20 tools for most applications**.

If more needed:
- Use namespacing to create logical groupings
- Implement umbrella tools that route to specialized sub-tools
- Use example-based selection

## Guidelines

1. Write descriptions that answer what, when, and what returns
2. Use consolidation to reduce ambiguity
3. Implement response format options for token efficiency
4. Design error messages for agent recovery
5. Establish consistent naming conventions
6. Limit tool count and use namespacing
7. Question whether each tool enables or constrains the model
8. Prefer primitive, general-purpose tools over specialized wrappers

---

**Created**: 2025-12-20 | **Version**: 1.1.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
