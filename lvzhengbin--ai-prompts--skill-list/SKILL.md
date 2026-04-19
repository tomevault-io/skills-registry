---
name: skill-list
description: List all available skills configured in AGENTS.md. Scan and display skills with their names, descriptions, and trigger commands. Triggers when user mentions "列出技能", "list skills", "可用技能", "show skills", "技能列表", or uses command /skill-list. Use when this capability is needed.
metadata:
  author: lvzhengbin
---

# Skill List

Scan and list all available skills from the project's `AGENTS.md` configuration file.

## Workflow

1. **Run the scan script** to extract skills from `AGENTS.md`:
   ```bash
   python3 .claude/skills/skill-list/scripts/scan_skills.py
   ```

2. **Parse the output** between `SKILL_LIST_OUTPUT_START` and `SKILL_LIST_OUTPUT_END` markers.

3. **Display results to user** in a friendly format:
   - Show the skills summary table
   - Highlight the command column (`/command-name`)
   - Remind user: "使用对应的 command 命令来触发技能"

4. **Confirm file output**: If content changed, Markdown document is saved to `assets/skill_list.md`. If no changes detected, file update is skipped.

## Output Format

Display to user like this:

```
## 📋 可用技能列表

| 技能名称 | 触发命令 | 说明 |
| :--- | :--- | :--- |
| skill-name | `/command` | Description... |

💡 **提示**: 使用上表中的「触发命令」来激活对应的技能。例如输入 `/skill-create` 来创建新技能。
```

## Bundled Resources

- `scripts/scan_skills.py`: Parses `AGENTS.md` and extracts skill information
- `assets/skill_list.md`: Generated Markdown output file

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lvzhengbin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
