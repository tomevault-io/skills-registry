---
name: sksgrm
description: 删除整个 skill 分组 Use when this capability is needed.
metadata:
  author: majiayu000
---

从对话上下文推断组名（如用户说"删掉 docs 组"、"移除整个 writing 分组"），**直接运行**：

```bash
python3 "$(git rev-parse --show-toplevel 2>/dev/null)/.claude/skills/sksgrm/grm.py" "<GROUP>"
```

组名不明确时才询问用户。

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-06 -->
