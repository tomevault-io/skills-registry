---
name: createcli
description: | Use when this capability is needed.
metadata:
  author: carlheath
---

# CreateCLI

**Role:** quick CLI authoring for `~/.claude/scripts/`.

## Stack choice

| Task | Stack | Reason |
|------|-------|--------|
| Files, glob, regex, JSON piping | bash | Fastest if < 50 lines |
| Anything complex, types, JSON handling | TypeScript (bun) | Default |
| Data-heavy operations, ML libraries | Python (uv) | Library access |

## Anatomy (TypeScript default)

```typescript
#!/usr/bin/env bun
/**
 * [scriptname] — [short description]
 *
 * Usage: [scriptname] [args] [flags]
 *
 * Created: 2026-MM-DD via createcli skill
 */

import { parseArgs } from 'util';

const { values, positionals } = parseArgs({
  args: process.argv.slice(2),
  options: {
    'dry-run': { type: 'boolean', default: true },  // destructive: default ON
    'verbose': { type: 'boolean', short: 'v' },
    'help': { type: 'boolean', short: 'h' },
  },
  allowPositionals: true,
});

if (values.help) {
  console.log(`[scriptname] — [description]

Usage:
  [scriptname] [args]

Flags:
  --dry-run     Show what would happen without doing it (default: ON for destructive)
  --verbose     More detailed output
  --help        This text
`);
  process.exit(0);
}

// ... core logic
```

## Anatomy (bash)

```bash
#!/usr/bin/env bash
set -euo pipefail

usage() {
  cat <<EOF
[scriptname] — [description]

Usage: [scriptname] [args]

Flags:
  -n, --dry-run    Show what would happen
  -v, --verbose    More detailed output
  -h, --help       This text
EOF
}

DRY_RUN=true  # destructive default
while [[ $# -gt 0 ]]; do
  case "$1" in
    -n|--dry-run) DRY_RUN=true; shift ;;
    --no-dry-run) DRY_RUN=false; shift ;;
    -v|--verbose) VERBOSE=true; shift ;;
    -h|--help) usage; exit 0 ;;
    *) ARGS+=("$1"); shift ;;
  esac
done

# core logic
```

## Rules

1. **Destructive = dry-run default ON.** Must use explicit `--no-dry-run` to execute.
2. **Log to `~/.claude/logs/[scriptname].jsonl`** for an audit trail when relevant (file-modifying, network-fetching, API calls).
3. **`chmod +x`** after Write — exec bit must be set.
4. **`--help` is mandatory** — show it even when no args.
5. **Never hardcode credentials** — read from `~/.claude/.env`.
6. **Output in markdown** if the script produces a text report (not tabular).

## Workflow

```
1. The user describes: "I need a script that [X]"
2. Choose stack (bash/TS/Python) per the table above
3. Create the anatomy template
4. Implement core logic
5. chmod +x ~/.claude/scripts/[scriptname]
6. Test: ./scripts/[scriptname] --help
7. Test: ./scripts/[scriptname] --dry-run [args]
8. Report to user: what the script does + examples
```

## Version history

- v3.0 (2026-05-02): initial public release.

---
> Source: [carlheath/ogmios](https://github.com/carlheath/ogmios) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
