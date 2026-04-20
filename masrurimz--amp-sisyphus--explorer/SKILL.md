---
name: explorer
description: Fast codebase search using Amp's Search tool for semantic search plus parallel Grep/glob for exact matches. Use when you need quick, comprehensive codebase reconnaissance. Use when this capability is needed.
metadata:
  author: masrurimz
---

# Explorer: Fast Codebase Search

Speed-optimized codebase search leveraging **Amp's built-in tools**.

## Tool Selection

| Need | Use | Why |
|------|-----|-----|
| **Semantic/conceptual search** | `Search` | AI-powered, understands code meaning |
| **Exact pattern match** | `Grep` | Fast regex, specific strings |
| **File discovery** | `glob` | Pattern-based file finding |
| **Read after locate** | `Read` | Get file contents |

## Primary Tool: `Search`

**USE SEARCH FIRST** for most searches. It's Amp's semantic search that understands code:

```
Search("Where is the authentication middleware implemented?")
Search("Find all API endpoints that handle user data")
Search("How does the payment processing flow work?")
```

### When Search excels:
- Finding implementations by concept
- Locating code by functionality 
- Understanding relationships between components
- Complex multi-step searches

## Secondary Tools: Grep + glob

Use for **exact matches** when you know the pattern:

```
# Parallel execution for speed
Grep("useAuth(")           # Exact function call
Grep("import.*useAuth")    # Import statements
glob("**/*auth*")          # Files with auth in name
```

## Execution Protocol

### Strategy 1: Semantic First
```
1. Search("conceptual question")     # Let AI find it
2. Read(found_files)                 # Get details
```

### Strategy 2: Parallel Exact Match
```
# When you know exact patterns - run in parallel
Grep("functionName(", path="src")
Grep("class ClassName", path="src")  
glob("**/*FileName*")
```

### Strategy 3: Combined
```
# Start semantic, refine with exact
Search("authentication implementation")
→ Found auth.ts, middleware.ts
Grep("validateToken", path="src/auth")  # Precise follow-up
```

## Quick Reference

| Task | Best Approach |
|------|---------------|
| Find where X is implemented | `Search("where is X implemented")` |
| Find all usages of function | `Grep("functionName(")` parallel with import grep |
| Find files by name pattern | `glob("**/*pattern*")` |
| Understand code flow | `Search("how does X flow work")` |
| Find exact string | `Grep("exact string", literal=true)` |

## Output Format

```
**Found: [description]**

| File | Line | Context |
|------|------|---------|
| `src/auth.ts` | 42 | `export function authenticate(...)` |

**Most relevant:** [file:line] - [why]
```

## Anti-Patterns

❌ Using Grep for conceptual searches (use Search)
❌ Sequential searches when parallel possible
❌ Reading files before finding them
❌ Ignoring Search for complex searches

## The Explorer Mantra

```
Search for concepts, Grep for patterns.
Parallel execution, fast results.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masrurimz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
