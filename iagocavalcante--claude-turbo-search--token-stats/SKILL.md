---
name: token-stats
description: Show token economics comparing usage with turbo-search vs without. Demonstrates actual savings from search-first approach. Use when this capability is needed.
metadata:
  author: iagocavalcante
---

# /token-stats - Token Economics Dashboard

Show the token savings achieved by using search-first exploration vs blind file reading.

## Instructions

When the user invokes `/token-stats`, analyze token usage and display savings.

### 1. Gather Activity Data

```bash
REPO_ROOT=$(git rev-parse --show-toplevel 2>/dev/null || echo "$PWD")
ACTIVITY_FILE="$REPO_ROOT/.claude-memory/activity.log"

# Count files read this session
if [ -f "$ACTIVITY_FILE" ]; then
    echo "=== Session Activity ==="
    FILES_READ=$(grep -c "READ:" "$ACTIVITY_FILE" 2>/dev/null || echo "0")
    FILES_EDITED=$(grep -c "EDIT:" "$ACTIVITY_FILE" 2>/dev/null || echo "0")
    SEARCHES=$(grep -c "SEARCH:" "$ACTIVITY_FILE" 2>/dev/null || echo "0")
    echo "Files read: $FILES_READ"
    echo "Files edited: $FILES_EDITED"
    echo "Searches performed: $SEARCHES"
else
    echo "No activity log found for this session"
fi
```

### 2. Calculate Codebase Stats

```bash
REPO_ROOT=$(git rev-parse --show-toplevel 2>/dev/null || echo "$PWD")

# Common exclusions for dependencies/build artifacts across languages
EXCLUDES=(
  # Node.js/JavaScript
  --glob '!node_modules/' --glob '!bower_components/' --glob '!.npm/'
  --glob '!.yarn/' --glob '!.pnpm-store/' --glob '!dist/' --glob '!build/' --glob '!coverage/'
  # Elixir
  --glob '!_build/' --glob '!deps/' --glob '!.elixir_ls/' --glob '!.fetch/'
  # Java
  --glob '!target/' --glob '!.gradle/' --glob '!.mvn/' --glob '!out/' --glob '!.settings/'
  # Ruby
  --glob '!vendor/' --glob '!.bundle/' --glob '!.gem/'
  # PHP
  --glob '!vendor/' --glob '!.composer/'
  # Python
  --glob '!__pycache__/' --glob '!.venv/' --glob '!venv/' --glob '!.tox/'
  --glob '!.mypy_cache/' --glob '!.pytest_cache/' --glob '!*.egg-info/'
  # General
  --glob '!.git/'
)

# File types to count
FILE_TYPES="--glob *.ts --glob *.js --glob *.tsx --glob *.jsx --glob *.py --glob *.go --glob *.rs --glob *.ex --glob *.exs --glob *.java --glob *.rb --glob *.php --glob *.md"

# Total files in codebase (excluding dependencies)
TOTAL_FILES=$(rg --files "${EXCLUDES[@]}" $FILE_TYPES "$REPO_ROOT" 2>/dev/null | wc -l | tr -d ' ')

# Estimate total tokens (count lines in source files)
TOTAL_LINES=$(rg --files "${EXCLUDES[@]}" $FILE_TYPES "$REPO_ROOT" 2>/dev/null | xargs wc -l 2>/dev/null | tail -1 | awk '{print $1}')
ESTIMATED_TOTAL_TOKENS=$((TOTAL_LINES * 4 / 3))  # ~1.33 tokens per word, ~3 words per line

echo ""
echo "=== Codebase Size ==="
echo "Total indexable files: $TOTAL_FILES"
echo "Total lines: $TOTAL_LINES"
echo "Estimated tokens: $ESTIMATED_TOTAL_TOKENS"
```

### 3. Query Memory for Historical Data

```bash
PLUGIN_DIR=$(find ~/.claude/plugins -name "claude-turbo-search" -type d 2>/dev/null | head -1)
[ -z "$PLUGIN_DIR" ] && PLUGIN_DIR="$HOME/claude-turbo-search"
MEMORY_SCRIPT="$PLUGIN_DIR/memory/memory-db.sh"

if [ -f "$MEMORY_SCRIPT" ]; then
    echo ""
    echo "=== Memory Stats ==="
    "$MEMORY_SCRIPT" stats 2>/dev/null || echo "Run /turbo-index first to initialize memory"

    echo ""
    echo "=== Token Metrics (All-Time) ==="
    "$MEMORY_SCRIPT" token-stats 2>/dev/null || echo "{}"
fi
```

### 4. Calculate and Display Economics

Based on the gathered data, calculate and present:

**Token Economics Model:**

| Scenario | Calculation | Typical Cost |
|----------|-------------|--------------|
| **Blind exploration** | Read 20+ files to find relevant code | ~50,000 tokens |
| **With turbo-search** | Search (50 tokens) + Read 3-5 targeted files | ~5,000 tokens |
| **Savings** | | **~90%** |

**Present this table to the user:**

```
╔══════════════════════════════════════════════════════════════╗
║                  TOKEN ECONOMICS DASHBOARD                    ║
╠══════════════════════════════════════════════════════════════╣
║                                                               ║
║  Codebase: [PROJECT_NAME]                                    ║
║  Total Files: [X] | Total Lines: [Y] | Est. Tokens: [Z]      ║
║                                                               ║
╠══════════════════════════════════════════════════════════════╣
║                     THIS SESSION                              ║
╠══════════════════════════════════════════════════════════════╣
║                                                               ║
║  Searches performed:     [N]     (~50 tokens each)           ║
║  Files read (targeted):  [M]     (~1,000 tokens each)        ║
║  Files edited:           [K]                                  ║
║                                                               ║
║  ─────────────────────────────────────────────────────────── ║
║                                                               ║
║  ESTIMATED USAGE WITH PLUGIN:     ~[X] tokens                ║
║  ESTIMATED WITHOUT (blind read):  ~[Y] tokens                ║
║                                                               ║
║  ✨ SAVINGS: ~[Z]% ([Y-X] tokens saved)                      ║
║                                                               ║
╠══════════════════════════════════════════════════════════════╣
║                   HISTORICAL (ALL SESSIONS)                   ║
╠══════════════════════════════════════════════════════════════╣
║                                                               ║
║  Tracked sessions:       [N]   (from token-stats JSON)       ║
║  Total searches:         [S]                                  ║
║  Total files read:       [R]                                  ║
║  Total files edited:     [E]                                  ║
║                                                               ║
║  All-time tokens used:   ~[U] tokens                         ║
║  All-time without plugin:~[W] tokens                         ║
║  All-time tokens saved:  ~[V] tokens                         ║
║                                                               ║
║  (No data yet? Use /remember to start tracking)              ║
║                                                               ║
╚══════════════════════════════════════════════════════════════╝
```

### 5. Token Calculation Logic

Use these estimates for calculations:

```
SEARCH_COST = 50              # tokens per qmd search
FILE_READ_COST = 1000         # avg tokens per file read
BLIND_EXPLORATION_FILES = 20  # files typically read without search
TARGETED_READ_FILES = 3       # files read with search-first approach

# With plugin
with_plugin = (SEARCHES * SEARCH_COST) + (FILES_READ * FILE_READ_COST)

# Without plugin (estimate)
without_plugin = BLIND_EXPLORATION_FILES * FILE_READ_COST

# Savings
savings_tokens = without_plugin - with_plugin
savings_percent = (savings_tokens / without_plugin) * 100
```

### 6. Pro Tips

End with actionable suggestions:

```
💡 Pro Tips to Maximize Savings:
   • Use 'qmd search' before reading any file
   • Run /remember at session end to build context
   • Check /memory-stats for accumulated knowledge
   • The more you use it, the smarter it gets!
```

## Notes

- Estimates are based on typical Claude token encoding
- Actual savings vary based on codebase structure and task type
- Historical data requires using /remember consistently

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iagocavalcante) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
