---
name: sema
description: Semantic code search and code intelligence. Use for 'where is X', 'how does Y work', 'find Z logic' queries, symbol definitions, references, and structural queries. Finds code by concept, not exact text. Use when this capability is needed.
metadata:
  author: jrc2139
---

# sema - Semantic Code Search & Code Intelligence

Requires sema v0.2.0+. First search automatically indexes your codebase (no manual setup needed). Use `sema "<query>"` for semantic code search. Hybrid search (semantic + keyword) is the default.

## Code Intelligence Commands

Direct database queries for symbol lookup and structural filtering. These require a previously built index (automatic on first search).

```bash
# Find symbol definitions
sema find <symbol>                   # Find by exact name
sema find <symbol> --prefix          # Prefix match
sema find <symbol> --kind function   # Filter by kind (function, class, method, etc.)
sema find <symbol> --exported        # Only exported symbols
sema find <symbol> --semantic        # Semantic search for similar symbols (requires server)
sema find <symbol> --refs            # Include references alongside definitions

# Find symbol references
sema refs <symbol>                   # Find chunks that reference a symbol

# Query by structural metadata
sema query --kind function --exported --no-docstring      # Undocumented public functions
sema query --kind method --min-complexity 10              # Complex methods
sema query --role test --language zig                     # All test code in Zig
sema query --parent MyClass --kind method                 # All methods in MyClass
```

### File Outline, Impact Analysis & Codebase Map

```bash
# Show file skeleton with symbol nesting
sema outline <file>                  # Compact view
sema outline <file> -v               # Include signatures

# Impact analysis: where is a symbol used?
sema blast <symbol>                  # Definition + grouped references

# Codebase map: cluster files by semantic similarity
sema map                             # Auto-detect cluster count
sema map --clusters 5                # Force 5 clusters
sema map --depth 2                   # Hierarchical sub-clustering
sema map --json                      # JSON output
```

### Query Filters

- `--kind <type>`: function, class, method, struct, enum, interface, etc.
- `--exported`: Only exported/public symbols
- `--no-docstring`: Symbols without documentation
- `--has-docstring`: Symbols with documentation
- `--min-complexity N`: Minimum cyclomatic complexity
- `--max-complexity N`: Maximum cyclomatic complexity
- `--role <role>`: definition, test, configuration
- `--parent <symbol>`: Filter by parent symbol
- `-l, --language <lang>`: Filter by language
- `-n <n>`: Max results (default: 5)

## Usage

```bash
# Hybrid search (default) - best for natural language questions
sema "where is authentication handled?"
sema "error handling patterns" -l python

# Keyword search (-k) - fast, no model loading, good for identifiers
sema -k "parseArgs"
sema -k "ConfigLoader"

# Search within a directory
sema "API endpoints" src/
sema "database queries" ./backend/

# With filters
sema "error handling" -l zig -n 10
sema -g "src/**/*.ts" "authentication"
sema --exclude "**/tests/*" "main function"
```

## Options

- `<path>`: Search within directory (positional, e.g., `sema "query" src/`)
- `-k, --keyword`: BM25 text search (no model loading, instant)
- `-n <num>`: Max results (default: 5)
- `-l <lang>`: Filter by language (python, javascript, zig, etc.)
- `-g <pattern>`: Filter by file path glob (e.g., "src/**/*.ts")
- `--exclude <pattern>`: Exclude files matching glob pattern (e.g., "**/tests/*", "*.md")
- `-c, --compact`: File paths only

## Search Modes

| Mode | Flag | Best For | First Run | Speed |
|------|------|----------|-----------|-------|
| Hybrid | (default) | Natural language questions | Keyword results immediately; run `sema index` for semantic | ~50ms (subsequent) |
| Keyword | `-k` | Exact identifiers, function names | FTS-only index built instantly, no model loading | ~5ms |

**Auto-Indexing**: On first search, sema builds a keyword (FTS) index automatically.
- **Keyword mode** (`-k`): FTS-only, instant, no model needed
- **Hybrid mode** (default): Shows keyword results. Run `sema index` for full semantic search
- Set `auto_embed: true` in config to auto-download model and embed on first search

**Server Lifecycle**: The server auto-starts when embeddings exist and auto-shuts down after 30 minutes of inactivity.

### Semantic Indexing (Agent Guidance)

If the user wants semantic (hybrid) search and no index exists yet, offer to run `sema index` in the background:

```bash
sema index .   # Downloads model on first run, then indexes
```

- **Ask permission first** -- indexing is CPU-intensive and can take minutes on large codebases
- **Run in background** -- use `run_in_background: true` so the user can keep working
- **Notify on completion** -- tell the user when indexing finishes
- **Set expectations** -- "This may take a few minutes on a large codebase and will use significant CPU while running"
- **Stop the server** -- if the user wants to free resources: `sema stop`
- **Check status** -- `sema list` shows running servers and `sema doctor` checks system health

## When to Use

**Use sema find:**
- "Show me the definition of parseConfig"
- "Where is AuthHandler defined?"
- "Find all exported functions starting with 'handle'"

**Use sema refs:**
- "Where is this function called?"
- "Find all references to this symbol"

**Use sema query:**
- "Show me all undocumented public functions"
- "Find complex methods (high cyclomatic complexity)"
- "List all test functions in this codebase"
- "Show me configuration-related symbols"

**Use sema outline:**
- "What symbols are in this file?"
- "Show me the structure of engine.zig"

**Use sema blast:**
- "What would break if I change this function?"
- "How widely is this symbol used?"

**Use sema map:**
- "Give me an overview of this codebase"
- "How is the code organized?"
- "What are the main components/modules?"

**Use sema (hybrid search):**
- "Where is authentication handled?"
- "How does Y work?"
- Finding code by concept/meaning

**Use sema -k (keyword search):**
- Searching for specific function/class names
- Finding exact identifiers
- When you know the exact term

**Use grep:**
- Exact string literals
- Regex patterns

## Configuration

Configure sema behavior in `.sema/config.json` or `~/.config/sema/config.json`:

**Search Mode** (`db.mode`):
- `"hybrid"` (default): Semantic + keyword search. Loads embedding model, best for natural language queries
- `"keyword"`: FTS-only search. No model loading, instant ~5ms startup, good for identifiers

**Embedding Models** (default: `e5-small-v2`):
- `e5-small-v2`: Small, 256 dimensions, balanced speed/quality (default)
- `e5-large-v2`: Larger model, better semantic understanding
- `embeddinggemma-300m`: Gemma-based embedder, good for domain-specific code

Example config:

```json
{
    "db": {
        "mode": "hybrid"
    }
}
```

## Ignore Patterns

By default, sema respects `.gitignore` files when indexing. You can also use `.semaignore` files for sema-specific exclusions (same syntax as `.gitignore`):

```bash
# .semaignore - exclude from sema but keep in git
generated/
*.min.js
vendor/
```

`.semaignore` files work at any directory level, just like `.gitignore`.

To index everything (ignore no patterns):

```bash
sema index --no-gitignore .
```

Or in config:

```json
{
    "respect_ignore": false
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jrc2139) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
