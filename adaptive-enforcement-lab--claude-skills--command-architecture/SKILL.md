---
name: command-architecture
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Command Architecture

## When to Use This Skill

A well-designed CLI has commands that work both independently and as part of larger workflows. This section covers:

- **[Orchestrator Pattern](orchestrator-pattern.md)** - Coordinate multi-step workflows
- **[Subcommand Design](subcommand-design.md)** - Build independently useful commands
- **[Input/Output Contracts](io-contracts.md)** - Design for pipelines and automation

---


## Implementation

See the full implementation guide in the [source documentation](https://adaptive-enforcement-lab.com/build/go-cli-architecture/).


## Key Principles

| Practice | Description |
| ---------- | ------------- |
| **Flat hierarchy** | Avoid deeply nested subcommands (max 2 levels) |
| **Verb-noun ordering** | `myctl restart deployment` not `myctl deployment restart` |
| **Consistent flags** | Use same flag names across commands |
| **Hidden internal commands** | Mark debugging commands as hidden |
| **Exit codes** | Use consistent exit codes (0=success, 1=failure, 2=usage error) |

---

*Design commands for both humans and scripts.*


## Examples

See [examples.md](examples.md) for code examples.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/build/go-cli-architecture/)
- [AEL Build](https://adaptive-enforcement-lab.com/build/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
