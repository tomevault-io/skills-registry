---
name: git-master
description: Git expert for atomic commits, rebasing, and history management Use when this capability is needed.
metadata:
  author: yeachan-heo
---

# Git Master Command

Routes to the git-master agent for git operations.

## Usage

```
/git-master <git task>
```

## Routing

```
delegate(role="git-master", tier="STANDARD", task="{{ARGUMENTS}}")
```

## Capabilities
- Atomic commits with conventional format
- Interactive rebasing
- Branch management
- History cleanup
- Style detection from repo history

Task: {{ARGUMENTS}}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yeachan-heo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
