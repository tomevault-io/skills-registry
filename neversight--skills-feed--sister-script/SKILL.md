---
name: sister-script
description: Document-first automation — the doc is the source of truth Use when this capability is needed.
metadata:
  author: neversight
---

# Sister Script

> **Document-first development. Automate only what's proven.**

The document is the source of truth. Scripts are its children.

> [!TIP]
> **LIFT stage of [play-learn-lift](../play-learn-lift/).** Proven procedures become automation.

## The Pattern

```mermaid
graph TD
    D[📄 Document] -->|manual test| C[💻 Commands]
    C -->|document| P[📋 Procedure]
    P -->|automate| S[🤖 Sister Script]
    S -->|improve| D
```

1. Start with natural language (PLAY)
2. Add manual commands (PLAY/LEARN)  
3. Document working procedures (LEARN)
4. Generate automation (LIFT)

## Bidirectional Evolution

- Document → Script: Proven procedures become automated
- Script → Document: Automation insights improve docs

## Contents

| File | Purpose |
|------|---------|
| [SKILL.md](./SKILL.md) | Full methodology documentation |
| [PROCEDURE.md.tmpl](./PROCEDURE.md.tmpl) | Procedure template |
| [SISTER.yml.tmpl](./SISTER.yml.tmpl) | Sister relationship template |

## The Intertwingularity

Sister-script is the LIFT stage of [play-learn-lift](../play-learn-lift/) — automate proven patterns.

```mermaid
graph LR
    SS[👯 sister-script] -->|LIFT stage of| PLL[🎮📚🚀 play-learn-lift]
    SS -->|automates| DOC[📄 documents]
    SS -->|produces| CODE[🤖 scripts]
    
    RN[📓 research-notebook] -->|feeds| SS
    SL[📜 session-log] -->|source for| SS
```

---

## Sniffable Python: The Structure

Sister scripts should follow [sniffable-python/](../sniffable-python/) conventions:

```python
#!/usr/bin/env python3
"""tool-name: One-line description.

Docstring becomes --help AND is visible to LLM.
"""

import argparse

def main():
    """CLI structure — sniff this to understand the tool."""
    parser = argparse.ArgumentParser(description=__doc__.split('\n')[0])
    # ... CLI tree here ...
    args = parser.parse_args()
    _dispatch(args)

# Implementation below the fold
```

**Why sniffable Python for sister scripts?**
- LLM can read `main()` and understand the CLI
- Human can run `--help` for the same info
- Single source of truth for documentation
- One sniff and you smell success

---

## Dovetails With

### Sister Skills
| Skill | Relationship |
|-------|--------------|
| [sniffable-python/](../sniffable-python/) | **The structure** sister scripts should follow |
| [play-learn-lift/](../play-learn-lift/) | Sister-script IS the LIFT stage |
| [session-log/](../session-log/) | Source material for patterns |
| [research-notebook/](../research-notebook/) | Documented procedures |
| [plan-then-execute/](../plan-then-execute/) | Scripts can become plans |

### Protocol Symbols
| Symbol | Link |
|--------|------|
| `SISTER-SCRIPT` | [PROTOCOLS.yml](../../PROTOCOLS.yml#SISTER-SCRIPT) |
| `BUILD-COMMAND` | [PROTOCOLS.yml](../../PROTOCOLS.yml#BUILD-COMMAND) |
| `PLAY-LEARN-LIFT` | [PROTOCOLS.yml](../../PROTOCOLS.yml#PLAY-LEARN-LIFT) |

### Navigation
| Direction | Destination |
|-----------|-------------|
| ⬆️ Up | [skills/](../) |
| ⬆️⬆️ Root | [Project Root](../../) |
| 🎮 Sister | [play-learn-lift/](../play-learn-lift/) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
