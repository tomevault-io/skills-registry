---
name: ultrawork
description: Maximum performance mode using Amp's built-in oracle, librarian, and Search tools in parallel for comprehensive analysis. Activates when you need deep, multi-dimensional research across codebase and external docs. Use when this capability is needed.
metadata:
  author: masrurimz
---

# Ultrawork: Maximum Performance Mode

Activates **parallel execution** of Amp's most powerful built-in tools for comprehensive analysis.

## Built-in Tools Used

| Tool | Purpose | Strength |
|------|---------|----------|
| `oracle` | Deep reasoning, architecture review | Expert analysis, code review |
| `librarian` | External documentation research | Library docs, best practices |
| `Search` | Semantic codebase search | Conceptual code discovery |
| `Grep` | Exact pattern matching | Fast, precise matches |
| `glob` | File pattern discovery | Structure mapping |

## Parallel Execution Pattern

```
┌─────────────────────────────────────────────────────┐
│                    ULTRAWORK                         │
├─────────────────────────────────────────────────────┤
│  Launch in parallel:                                 │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │  Search  │  │ librarian│  │  oracle  │          │
│  │(internal)│  │(external)│  │ (expert) │          │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘          │
│       │             │             │                 │
│       ▼             ▼             ▼                 │
│  Codebase      External       Architecture          │
│  locations     best           recommendations       │
│               practices                              │
│       │             │             │                 │
│       └─────────────┴─────────────┘                 │
│                     │                               │
│                     ▼                               │
│           SYNTHESIS: Combined insights              │
└─────────────────────────────────────────────────────┘
```

## Usage

### Comprehensive Codebase Audit

```
# Step 1: Parallel discovery
Search("Find all authentication-related code")
Search("Find all API endpoints")  
Search("Find all database queries")

# Step 2: Expert analysis
oracle(
  task: "Audit this codebase for security vulnerabilities",
  files: [discovered_files]
)

# Step 3: External research
librarian(
  query: "OWASP security best practices for Node.js APIs"
)
```

### Deep Feature Analysis

```
# Parallel internal search
Search("How does the payment processing work?")
Grep("processPayment(", path="src")
glob("**/payment*")

# Parallel external research
librarian(query: "Stripe API integration patterns")
librarian(query: "Payment processing error handling best practices")

# Expert synthesis
oracle(
  task: "Review payment implementation against best practices",
  files: ["src/payments/"],
  context: "Compare against Stripe patterns from librarian research"
)
```

### Multi-Repository Research

```
# Use librarian for cross-repo analysis
librarian(
  query: "How does the Kubernetes scheduler handle pod affinity?",
  context: "Comparing to our scheduler implementation"
)

librarian(
  query: "How does React implement concurrent rendering?",
  context: "Understanding patterns for our UI framework"
)
```

## When to Use Ultrawork

✅ **Use for:**
- "Audit the entire codebase for X"
- "Find all instances and best practices for Y"
- "Research and analyze how we should implement Z"
- "Compare our implementation against industry standards"
- "Deep dive into module X with external context"

❌ **Don't use for:**
- Simple file searches (use Search directly)
- Single-file edits (just edit it)
- Tasks with clear, linear steps (use sisyphus)

## Execution Checklist

```
1. [ ] Identify all dimensions to research
2. [ ] Launch Search queries for internal code (parallel)
3. [ ] Launch librarian queries for external docs (parallel)
4. [ ] Collect results
5. [ ] Call oracle for synthesis/review
6. [ ] Combine into actionable insights
```

## Output Format

```markdown
## Ultrawork Analysis: [Topic]

### Internal Findings (Search)
- Found X in [files...]
- Pattern Y appears in [locations...]

### External Research (librarian)
- Best practice: [summary]
- Industry standard: [summary]
- Relevant documentation: [links]

### Expert Analysis (oracle)
- Recommendation: [summary]
- Trade-offs: [list]
- Risks: [list]

### Synthesized Recommendations
1. [Action item with rationale]
2. [Action item with rationale]
```

## Anti-Patterns

❌ Running tools sequentially when parallel is possible
❌ Skipping oracle for complex decisions
❌ Using Task for research (use librarian)
❌ Guessing patterns (use Search for semantic search)

## The Ultrawork Mantra

```
All tools, all dimensions, all at once.
Search finds, librarian researches, oracle advises.
Synthesize into action.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masrurimz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
