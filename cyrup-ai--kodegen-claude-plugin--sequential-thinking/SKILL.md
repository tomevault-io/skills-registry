---
name: sequential-thinking
description: > Use when this capability is needed.
metadata:
  author: cyrup-ai
---

# Sequential Thinking - Structured Reasoning

## Core Concept

`mcp__plugin_kg_kodegen__sequential_thinking` tracks your reasoning process through numbered thoughts. It supports revising earlier conclusions, branching to explore alternatives, and dynamically adjusting your plan as understanding deepens.

## Key Parameters

**Required:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `thought` | string | Your current thinking step |
| `thought_number` | number | Current thought (1-based) |
| `total_thoughts` | number | Estimated total needed |
| `next_thought_needed` | boolean | Whether more steps needed |

**Optional:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `is_revision` | boolean | Marks thought as revising earlier thinking |
| `revises_thought` | number | Which thought being reconsidered |
| `branch_from_thought` | number | Create branch from this thought |
| `branch_id` | string | Identifier for the branch |
| `needs_more_thoughts` | boolean | Extend beyond total_thoughts |

## Usage Examples

### Simple Linear Reasoning
```json
// Step 1
{
  "thought": "First, I need to understand the database schema",
  "thought_number": 1,
  "total_thoughts": 4,
  "next_thought_needed": true
}

// Step 2
{
  "thought": "The schema has 3 tables: users, orders, products",
  "thought_number": 2,
  "total_thoughts": 4,
  "next_thought_needed": true
}

// Step 3
{
  "thought": "The query should join users and orders on user_id",
  "thought_number": 3,
  "total_thoughts": 4,
  "next_thought_needed": true
}

// Final step
{
  "thought": "Final solution: SELECT * FROM users JOIN orders ON users.id = orders.user_id",
  "thought_number": 4,
  "total_thoughts": 4,
  "next_thought_needed": false
}
```

### Revision Pattern
```json
// Initial approach
{
  "thought": "I'll use a recursive algorithm",
  "thought_number": 1,
  "total_thoughts": 3,
  "next_thought_needed": true
}

// Realize issue
{
  "thought": "Wait, recursion will cause stack overflow for large inputs",
  "thought_number": 2,
  "total_thoughts": 4,
  "next_thought_needed": true
}

// Revise approach
{
  "thought": "Revising: use iterative approach with explicit stack instead",
  "thought_number": 3,
  "total_thoughts": 4,
  "is_revision": true,
  "revises_thought": 1,
  "next_thought_needed": true
}
```

### Branching for Alternatives
```json
// Identify options
{
  "thought": "Two approaches possible: microservices vs monolith",
  "thought_number": 1,
  "total_thoughts": 5,
  "next_thought_needed": true
}

// Branch A
{
  "thought": "Microservices: better scaling, more complexity",
  "thought_number": 2,
  "total_thoughts": 5,
  "branch_from_thought": 1,
  "branch_id": "microservices",
  "next_thought_needed": true
}

// Branch B
{
  "thought": "Monolith: simpler deployment, harder to scale",
  "thought_number": 3,
  "total_thoughts": 5,
  "branch_from_thought": 1,
  "branch_id": "monolith",
  "next_thought_needed": true
}
```

### Dynamic Adjustment
```json
// Start with estimate
{
  "thought": "Initial analysis suggests 3 steps",
  "thought_number": 1,
  "total_thoughts": 3,
  "next_thought_needed": true
}

// Realize more depth needed
{
  "thought": "This is more complex, need 6 steps total",
  "thought_number": 2,
  "total_thoughts": 6,
  "next_thought_needed": true
}
```

## When to Use What

| Problem Type | Use Sequential Thinking? | Why |
|--------------|-------------------------|-----|
| Complex algorithm design | Yes | Multi-step with revision |
| Simple file read | No | No reasoning needed |
| Architecture decisions | Yes | Branch to explore options |
| Bug investigation | Yes | Iterative hypothesis testing |
| Direct code generation | No | Just write the code |

## Output Format

Each call returns:
- `thought_number`: Current position
- `total_thoughts`: Estimated total
- `next_thought_needed`: Boolean
- `branches`: List of branch IDs
- `thought_history_length`: Total recorded

## Remember

- **Start with reasonable estimate** - can adjust total_thoughts
- **Mark revisions explicitly** - use is_revision and revises_thought
- **Branch for alternatives** - explore multiple paths
- **Be specific** - each thought should represent clear progress
- **Conclude properly** - set next_thought_needed: false when done
- **State persists automatically** - just keep calling, it remembers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyrup-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
