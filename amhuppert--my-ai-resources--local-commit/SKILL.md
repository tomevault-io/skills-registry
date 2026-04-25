---
name: local-commit
description: This skill should be used when the user wants to commit changes to the local private repository using lgit. It manages versioning of private AI configuration files. Use when this capability is needed.
metadata:
  author: amhuppert
---

# Local Private Repository Commit

Description of private versioning pattern: !`echo $HOME`/.claude/agent-docs/local-files-pattern.md

This command commits changes to the local private repository using the lgit wrapper.

!`lgit add .`

```bash
% lgit status
!`lgit status`
% lgit diff --cached
!`lgit diff --cached`
```

Think hard about the changes and generate a descriptive commit message.

Commit message format:

```
{one line commit summary}

{more detailed description of changes}
```

Then commit the changes using `lgit commit -m ...`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amhuppert) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
