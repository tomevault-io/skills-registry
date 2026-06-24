---
name: know
description: Display all available Knowledge commands organized by group. Usage: /know, /aide, /? Use when this capability is needed.
metadata:
  author: packetqc
---

## /know — Knowledge Command Reference

Displays the full command reference: all groups and commands with descriptions.

### Execute

!`python3 -c "
import subprocess, os, sys
R = 'Knowledge/K_TOOLS' if os.path.isdir('Knowledge/K_TOOLS/scripts') else '.'
result = subprocess.run(['python3', R+'/scripts/help_command.py'], capture_output=False)
sys.exit(result.returncode)
"`

**MANDATORY**: The script output above contains the full command reference in markdown tables. Claude MUST output ALL section headers and tables as its own visible text — tool output is collapsed in the UI and invisible to the user. Copy every `### Section` and its table verbatim into your response. Do NOT summarize or condense — show the full tables.

### Contextual Help

For contextual help on any command, use `<cmd> ?`:

!`python3 -c "
import subprocess, os, sys
R = 'Knowledge/K_TOOLS' if os.path.isdir('Knowledge/K_TOOLS/scripts') else '.'
args = '$ARGUMENTS'.strip()
if args and args.endswith('?'):
    cmd = args.rstrip('? ').strip()
    result = subprocess.run(['python3', R+'/scripts/help_contextual.py', cmd], capture_output=False)
    sys.exit(result.returncode)
"`

---
> Source: [packetqc/knowledge](https://github.com/packetqc/knowledge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
