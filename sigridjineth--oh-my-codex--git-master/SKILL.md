---
name: git-master
description: Git expert for atomic commits, rebasing, and history management Use when this capability is needed.
metadata:
  author: sigridjineth
---

# Git Master Command

Routes to the git-master agent for git operations.

## Usage

```
/oh-my-codex:git-master <git task>
```

## Routing

```
Task(subagent_type="oh-my-codex:git-master", model="sonnet", prompt="{{ARGUMENTS}}")
```

## Capabilities
- Atomic commits with conventional format
- Interactive rebasing
- Branch management
- History cleanup
- Style detection from repo history

Task: {{ARGUMENTS}}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sigridjineth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
