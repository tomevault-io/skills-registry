---
name: skill-web-bundle
description: Package skills into web-compatible bundles. Converts any skill directory into a single .txt file containing all resources, making it usable in web-based AI chatbots. Validates skill structure and warns about script compatibility. Triggers when user mentions "skill-web-bundle", "打包skill", "web bundle", or wants to create web bundles of skills for external use. Use when this capability is needed.
metadata:
  author: lvzhengbin
---

# Skill Web Bundle

Package skills into web-compatible text bundles for use in external AI chatbots.

## Overview

This skill converts any skill directory into a single `.txt` file containing all resources (SKILL.md, scripts, references, templates, assets) in a structured format that can be used in web-based AI environments where file system access is limited.

## Usage

```
/skill-web-bundle <skill-name>
```

**Example:**
```
/skill-web-bundle track-design
/skill-web-bundle skill-creator
```

## Workflow

### Step 1: Validate Input

1. **Self-reference check**: Ensure target skill is not `skill-web-bundle` itself
   - If user tries to bundle `skill-web-bundle`, show error: "❌ 错误：不能打包skill-web-bundle自己"
   
2. **Skill existence check**: Verify the skill directory exists at `.claude/skills/<skill-name>`
   - If not found, show error: "❌ 错误：找不到skill目录: .claude/skills/<skill-name>"

3. **Skill structure validation**: Verify the skill follows standard directory structure
   - Required: `SKILL.md` file must exist
   - Required: SKILL.md must contain YAML frontmatter with `name` and `description` fields
   - If validation fails, show specific error message

### Step 2: Script Warning

If `scripts/` directory exists and contains files:
1. List the script files found
2. Display warning message:
   ```
   ⚠️  警告：检测到scripts脚本文件
   在web环境中可能无法正常执行skill功能。
   确定是否要继续web bundle打包？(y/n)
   ```
3. Wait for user confirmation
4. If user declines, exit gracefully

### Step 3: Execute Bundling

Run the bundling script:
```bash
python3 .claude/skills/skill-web-bundle/scripts/bundle_skill.py \
    --skill-path .claude/skills/<skill-name> \
    --output .claude/skills/skill-web-bundle/assets/
```

The script will:
- Read all files from the skill directory
- Apply the web bundle template
- Generate a single .txt file with all content

### Step 4: Output Confirmation

Display success message:
```
✅ Web bundle打包成功！
📦 产物路径: .claude/skills/skill-web-bundle/assets/<skill-name>.txt
💡 该文件可直接复制到任何web端AI聊天机器人中使用
```

## Bundle Format

The generated `.txt` file contains:

1. **Web Agent Instructions**: Explains how to operate as the bundled skill
2. **Resource Navigation Guide**: How to find and use bundled resources
3. **Bundled Content**: All skill files with clear START/END markers

Each file is wrapped with delimiters:
```
==================== START: skills/<skill-name>/<path>/<filename> ====================
[file content]
==================== END: skills/<skill-name>/<path>/<filename> ====================
```

## Error Handling

- **Self-bundling**: Cannot bundle `skill-web-bundle` itself
- **Missing SKILL.md**: Skill must have valid SKILL.md with metadata
- **Invalid metadata**: SKILL.md must contain `name` and `description` in frontmatter
- **Missing skill**: Target skill directory must exist

## Resources

- **Bundling Script**: `scripts/bundle_skill.py` - Python script for validation and bundling
- **Bundle Template**: `templates/web_bundle_template.txt` - Template for generated bundles
- **Output Directory**: `assets/` - Where generated bundles are stored

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lvzhengbin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
