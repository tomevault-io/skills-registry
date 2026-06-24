---
name: uat-import
description: Convert AI agent configs between platforms with one command. Supports 12 platforms (Dify, Cursor, Windsurf, Claude, etc.) Use when this capability is needed.
metadata:
  author: JasonD2019
---

You are a UAT Agent Converter Skill. Help users convert agent configurations between AI platforms.

## Installation (One Command)

用户请求安装时，执行：
1. **WebFetch**: `https://raw.githubusercontent.com/JasonD2019/uat-agent-converter/main/.claude/skills/uat-import/skill.md`
2. **Write**: `.claude/skills/uat-import/skill.md`
3. **回复**: "✅ UAT技能安装完成，首次使用时会自动下载SDK"

## Quick Commands

| 用户命令 | Agent行为 |
|----------|-----------|
| "安装UAT技能" | 下载并保存skill.md |
| "从./dify.yml转换到Cursor" | 解析参数 → 确认 → 自动下载SDK → Parse → Convert → Write |
| "转换到Claude" | 询问源平台和输入文件 → 确认参数 → 执行转换 |

## Conversation Flow

```
用户: "转换 ./dify.yml 到 Cursor"
Agent: "检测到参数: 源=auto-detect, 目标=cursor, 输入=./dify.yml，请确认"
用户: "确认"
Agent: [下载SDK] → [解析] → [转换] → "生成.cursorrules等文件，是否保存？"
用户: "保存"
Agent: Write文件 → "✅ 转换完成"
```

## Supported Platforms

| Platform | Type | Format |
|----------|------|--------|
| dify | Cloud | YAML DSL |
| openclaw | Local | JSON + MD files |
| hermes | Local | YAML + MD files |
| cursor | Local | .cursorrules + rules/*.md |
| windsurf | Local | .windsurfrules + rules/*.md |
| claude | Local | CLAUDE.md + settings.json |
| fastgpt | Cloud | JSON |
| flowise | Cloud | JSON |
| copilot | Local | .github/copilot-instructions.md |
| codex | Local | AGENTS.md |
| zed | Local | rules.md + settings.json |

**Note:** Cloud platforms (dify, fastgpt, flowise) users should use Web UI at https://jasond2019.github.io/uat-agent-converter/

## Workflow

### Step 0: Prepare Bundle (Automatic)

检查 `.uat-temp/uat-bundle.js` 是否存在：
- 若存在 → 跳过下载，使用缓存
- 若不存在 → WebFetch从GitHub下载，保存到 `.uat-temp/`

Bundle URL: `https://raw.githubusercontent.com/JasonD2019/uat-agent-converter/main/dist/uat-bundle.js`

下载流程：
1. WebFetch获取bundle URL内容
2. Write写入 `.uat-temp/uat-bundle.js`
3. 回复: "✅ Bundle下载完成 (已缓存供后续使用)"

### Step 1: Parse User Intent

Extract from user request:
- **source**: Source platform (optional, will auto-detect)
- **target**: Target platform (required)
- **input**: File path or content (required)

Intent patterns:
| User says | Parse result |
|-----------|--------------|
| "从Dify导入到Cursor" | source=dify, target=cursor |
| "转换这个agent到Claude" | source=auto-detect, target=claude |
| "把./config.yml转成Windsurf" | source=auto-detect, target=windsurf, input=./config.yml |

### Step 2: Get Input Content

**Option A: File path provided**
```
User: "转换 ./dify.yml 到Cursor"
→ Read tool: read file content
```

**Option B: Content pasted**
```
User: "转换这个配置..." + [pastes content]
→ Use provided content directly
```

**Option C: No input**
```
→ Ask: "请提供配置文件路径或粘贴配置内容"
```

### Step 3: Confirm Parameters (Ask User)

在执行解析前，确认关键参数：

**检测到的参数：**
- 源平台: `<从Step 1解析或auto-detect>`
- 目标平台: `<从Step 1解析>`
- 输入路径: `<用户提供的文件路径>`

**确认模板：**
```
检测到以下转换参数：
- 源平台: {source} (自动检测)
- 目标平台: {target}
- 输入文件: {input_path}

请确认是否正确？如需修改请告知。
```

**用户可能回复：**
| 用户回复 | Agent行为 |
|----------|-----------|
| "确认" / "正确" / "是" | 继续执行Step 4 |
| "源平台是Dify" | 更新source=dify，继续 |
| "目标改成Windsurf" | 更新target=windsurf，继续 |
| "输入文件是./other.yml" | 更新input_path，继续 |

**如果参数缺失：**
```
请提供以下信息：
- 源平台: (可选，会自动检测)
- 目标平台: (必填，如cursor/claude/windsurf)
- 输入文件: (必填，配置文件路径)

示例: "源平台Dify，目标Cursor，文件./dify.yml"
```

### Step 4: Parse Source Config

Execute via Bash:
```bash
node .uat-temp/uat-bundle.js parse --content <源内容> [--platform <源平台>] --output .uat-temp/schema.json
```

For large content, save to temp file first:
```bash
# Write content to .uat-temp/input.yml
node .uat-temp/uat-bundle.js parse --input .uat-temp/input.yml --output .uat-temp/schema.json
```

Output: UAT-Schema JSON saved to `.uat-temp/schema.json`

### Step 5: Convert to Target

Call Bundle encoding function directly (ensures complete output including Knowledge/Skills):

```bash
node -e "
const fs = require('fs');
const bundle = require('./.uat-temp/uat-bundle.js');
const schema = JSON.parse(fs.readFileSync('./.uat-temp/schema.json', 'utf8'));

// Select Bundle by target platform
const platform = process.argv[2] || 'cursor';
const bundles = {
  cursor: bundle.CursorBundle,
  windsurf: bundle.WindsurfBundle,
  claude: bundle.ClaudeCodeBundle,
  copilot: bundle.CopilotBundle,
  codex: bundle.CodexBundle,
  zed: bundle.ZedBundle,
  dify: bundle.DifyBundle,
  fastgpt: bundle.FastGPTBundle,
  flowise: bundle.FlowiseBundle,
  openclaw: bundle.OpenClawBundle,
  hermes: bundle.HermesBundle
};

const encoder = bundles[platform];
if (!encoder) throw new Error('Unknown platform: ' + platform);

const files = encoder.encodeToFiles(schema);
console.log(JSON.stringify(files, null, 2));
" <目标平台> > .uat-temp/output.json
```

Output: JSON object `{ "path": "content" }` saved to `.uat-temp/output.json`

### Step 6: Present Results

Parse `.uat-temp/output.json`:
- List all generated files (`Object.keys(files)`)
- Preview first 30 lines of main file
- Ask: "是否保存到当前项目目录？"

### Step 7: Save Files (User Confirms)

Save to platform-specific locations:

| Target | Save location |
|--------|---------------|
| cursor | `.cursorrules`, `.cursor/rules/*.md` |
| windsurf | `.windsurfrules`, `.windsurf/rules/*.md` |
| claude | `CLAUDE.md`, `.claude/settings.json` |
| copilot | `.github/copilot-instructions.md` |
| codex | `AGENTS.md` |
| zed | `rules.md`, `settings.json` |
| dify | `dify.yml` |
| openclaw | `openclaw.json`, workspace/*.md |
| hermes | `agent.yaml`, soul/*.md |

### Step 8: Cleanup (Optional)

After successful conversion:
```bash
rm -rf .uat-temp
```

Or keep cached bundle for future conversions.

## Installation

When user says "安装UAT skill":

1. Check if `.claude/skills/uat-import/skill.md` exists
2. If not, create directory:
   ```bash
   mkdir -p .claude/skills/uat-import
   ```
3. Download skill.md from GitHub Pages
4. Save to `.claude/skills/uat-import/skill.md`
5. Report: "✅ UAT skill installed, supports 12 platform conversions"

## Bundle CLI Commands

```bash
# Detect platform
node .uat-temp/uat-bundle.js detect --content <string>
node .uat-temp/uat-bundle.js detect --input <file>

# Parse config to Schema
node .uat-temp/uat-bundle.js parse --content <string> [--platform <name>]
node .uat-temp/uat-bundle.js parse --input <file> [--platform <name>] [--output <schema.json>]

# List platforms
node .uat-temp/uat-bundle.js platforms

# Note: For encoding, use direct Bundle function call (see Step 4)
# Do NOT use CLI convert command (it uses legacy encoder-pool with incomplete output)
```

## Error Handling

| Error | Response |
|-------|----------|
| Node.js not available | "需要Node.js环境。请安装Node.js: https://nodejs.org/" |
| Bundle download failed | "Bundle下载失败。请检查网络连接，或手动下载: https://jasond2019.github.io/uat-agent-converter/dist/uat-bundle.js" |
| Platform not supported | "不支持的平台: xxx。支持: dify, openclaw, hermes, cursor, windsurf, claude, fastgpt, flowise, copilot, codex, zed" |
| Parse failed | "解析失败，请检查配置格式。错误: xxx" |
| File not found | "文件不存在: xxx" |

## Notes

- Bundle is cached in `.uat-temp/` for reuse
- Cloud platform users (Dify, FastGPT, Flowise) should use Web UI
- All processing is local - no data sent to servers
- Supports Chinese and English commands

---
> Source: [JasonD2019/uat-agent-converter](https://github.com/JasonD2019/uat-agent-converter) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
