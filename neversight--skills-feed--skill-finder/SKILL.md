---
name: skill-finder
description: Full-featured Agent Skills management: Search 35+ skills, install locally, star favorites, update from sources. Supports tag search (#azure #bicep), category filtering, and similar skill recommendations. Use when this capability is needed.
metadata:
  author: neversight
---

# Skill Finder

Full-featured Agent Skills management tool with search, install, star, and update capabilities.

## When to Use

- Looking for skills for a specific task or domain
- Finding and installing skills locally
- Managing favorite skills with star feature
- Keeping your skill index up-to-date
- Discovering similar skills by category

## Features

- 🔍 **Search** - Local index (35+ skills) + GitHub API + Web fallback
- 🏷️ **Tags** - Search by category tags (`#azure #bicep`)
- 📦 **Install** - Download skills to local directory
- ⭐ **Star** - Mark and manage favorite skills
- 📊 **Stats** - View index statistics
- 🔄 **Update** - Sync all sources from GitHub
- 💡 **Similar** - Get category-based recommendations

## Quick Start

### Search

```bash
# Keyword search
python scripts/search_skills.py "pdf"
pwsh scripts/Search-Skills.ps1 -Query "pdf"

# Tag search (filter by category)
python scripts/search_skills.py "#azure #development"
pwsh scripts/Search-Skills.ps1 -Query "#azure #bicep"
```

### Skill Management

```bash
# Show detailed info (includes SKILL.md content)
python scripts/search_skills.py --info skill-name

# Install to local directory
python scripts/search_skills.py --install skill-name

# Star favorite skills
python scripts/search_skills.py --star skill-name
python scripts/search_skills.py --list-starred
```

### Index Management

```bash
# Update all sources
python scripts/search_skills.py --update

# Add new source repository
python scripts/search_skills.py --add-source https://github.com/owner/repo

# View statistics
python scripts/search_skills.py --stats
```

### List Options

```bash
python scripts/search_skills.py --list-categories
python scripts/search_skills.py --list-sources
python scripts/search_skills.py --similar skill-name
```

### Add New Source

When you find a good repository, add it to your index:

```bash
python scripts/search_skills.py --add-source https://github.com/owner/repo
pwsh scripts/Search-Skills.ps1 -AddSource -RepoUrl "https://github.com/owner/repo"
```

This will:

1. Add the repository as a source
2. Search for skills in `skills/`, `.github/skills/`, `.claude/skills/`
3. Auto-add found skills to your index

## Command Reference

| Command           | Description                                |
| ----------------- | ------------------------------------------ |
| `--info SKILL`    | Show skill details with SKILL.md content   |
| `--install SKILL` | Download skill to ~/.skills or custom dir  |
| `--star SKILL`    | Add skill to favorites                     |
| `--unstar SKILL`  | Remove from favorites                      |
| `--list-starred`  | Show all starred skills                    |
| `--similar SKILL` | Find skills with matching categories       |
| `--stats`         | Show index statistics                      |
| `--update`        | Update all sources from GitHub             |
| `--check`         | Verify tool dependencies (gh, curl)        |
| `#tag` in query   | Filter by category (e.g., `#azure #bicep`) |

## Popular Repositories

**Note:** These are representative examples. For the complete list, run `--list-sources` or check `sources` array in skill-index.json.

### Official (type: `official`)

- [anthropics/skills](https://github.com/anthropics/skills) - Official Claude Skills by Anthropic
- [github/awesome-copilot](https://github.com/github/awesome-copilot) - Official Copilot resources by GitHub

### Curated Lists (type: `awesome-list`)

- [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) - Curated Claude Skills

### Community (type: `community`)

- [obra/superpowers](https://github.com/obra/superpowers) - High-quality skills, agents, commands
- And many more... (run `--list-sources` for full list)

## Categories

**Dynamically extracted from skill-index.json.** Run `--list-categories` for current list.

Common categories include: `development`, `testing`, `document`, `azure`, `web`, `git`, `agents`, `mcp`, `cloud`, `creative`, `planning`, etc.

## Files

| File                             | Description               |
| -------------------------------- | ------------------------- |
| `scripts/Search-Skills.ps1`      | PowerShell script         |
| `scripts/search_skills.py`       | Python script             |
| `references/skill-index.json`    | Skill index (220+ skills) |
| `references/starred-skills.json` | Your starred skills       |

## Requirements

### Required

| Tool           | Version | Purpose                           | Install                                   |
| -------------- | ------- | --------------------------------- | ----------------------------------------- |
| **GitHub CLI** | 2.0+    | Search/install skills from GitHub | [cli.github.com](https://cli.github.com/) |
| **curl**       | Any     | Download files                    | Pre-installed on most systems             |

### Optional (choose one)

| Runtime    | Version | Script                      |
| ---------- | ------- | --------------------------- |
| PowerShell | 7+      | `scripts/Search-Skills.ps1` |
| Python     | 3.8+    | `scripts/search_skills.py`  |

### Setup

```bash
# 1. Install GitHub CLI
# Windows (winget)
winget install GitHub.cli

# macOS (Homebrew)
brew install gh

# Linux (apt)
sudo apt install gh

# 2. Authenticate with GitHub
gh auth login

# 3. Verify installation
gh --version
curl --version
```

### Verify Dependencies

```bash
# Check all dependencies at once
python scripts/search_skills.py --check
pwsh scripts/Search-Skills.ps1 -Check
```

**Expected output:**

```
✅ gh: installed (version 2.x.x)
✅ curl: installed
✅ All dependencies satisfied
```

### Troubleshooting

| Issue                   | Solution                                    |
| ----------------------- | ------------------------------------------- |
| `gh: command not found` | Install GitHub CLI and add to PATH          |
| `gh auth login` fails   | Run `gh auth login` and follow prompts      |
| Rate limit exceeded     | Wait or use authenticated requests          |
| curl SSL errors         | Update curl or check network/proxy settings |

## Agent Instructions

> ⚠️ **CRITICAL**: AI agents MUST follow these instructions. Prefer action proposals over verbose explanations.

### Core Principle

**Use "Do it? Yes/No?" style proposals.**

- ❌ Bad: "If you want to add new skills, you can run the following command..."
- ✅ Good: "Update the index?"

### Recommendation Workflow

When user asks for recommendations (e.g., "おすすめは？", "何かいいスキルある？"), suggest skills based on persona:

**Note:** These are reference examples. Always verify skill availability in skill-index.json before recommending.

| Persona          | Categories                      | Example Skills (verify in index)                                   |
| ---------------- | ------------------------------- | ------------------------------------------------------------------ |
| オフィスワーカー | document, office, communication | docx, xlsx, pptx, pdf, internal-comms, brainstorming               |
| 開発者           | development, testing, git       | test-driven-development, systematic-debugging, using-git-worktrees |
| Azure エンジニア | azure, development              | azure-env-builder, mcp-builder                                     |
| デザイナー       | design, creative, web           | brand-guidelines, canvas-design, frontend-design                   |
| 初心者           | meta, planning                  | skill-creator, brainstorming, writing-plans                        |

**Response Format:**

1. Ask about user's role/context if unclear
2. Show top 3-5 skills with descriptions
3. Include source breakdown table
4. Propose next actions

### Skill Search Workflow

1. **Search ALL sources in local index**

   - Read `references/skill-index.json`
   - **ALWAYS search ALL sources** (anthropics-skills, obra-superpowers, composio-awesome, etc.)
   - Check `lastUpdated` field
   - Suggest matching skills from every source

2. **🌟 Recommend from results (when multiple hits)**

   When search returns 3+ skills, pick the BEST one and explain why:

   ```
   ### 🌟 おすすめ: {skill-name}

   {理由: 公式スキル、機能が豊富、人気が高い、用途にマッチ など}
   ```

   **Selection criteria (in order):**

   1. **Official source** - anthropics-skills, github-awesome-copilot are preferred
   2. **Feature richness** - More capabilities = better
   3. **Relevance** - Best match for user's stated purpose
   4. **Recency** - Recently updated skills preferred

3. **If not found → Propose web search**

   ```
   Not found locally. Search the web?
   → GitHub: https://github.com/search?q=path%3A**%2FSKILL.md+{query}&type=code
   ```

4. **🚨 MANDATORY: After returning results → Propose next actions**

   **This step is NOT optional. ALWAYS include the proposal block below.**

   | Situation            | Proposal                                        |
   | -------------------- | ----------------------------------------------- |
   | Skill found          | "Install it?"                                   |
   | Good repo discovered | "Add to sources?"                               |
   | lastUpdated > 7 days | "⚠️ Index outdated. Update?" (strongly suggest) |
   | lastUpdated ≤ 7 days | "🔄 Update index?" (always show)                |

### 🚨 Mandatory Proposal Block

**ALWAYS include this block at the end of every search response. No exceptions.**

**CRITICAL: Do NOT show commands. Agent executes directly. Keep proposals SHORT.**

**Index update option MUST always be shown with date, regardless of how recent it is.**

```
**Next?**
1. 📦 Install? (which skill?)
2. 🔍 Details?
3. 🔄 Update index? (last: {date})       ← ALWAYS show
   ⚠️ If > 7 days: "Index outdated!"    ← Add warning
4. 🌐 Web search?
5. ➕ Add source?
```

### Checklist Before Responding

Before sending a search result response, verify:

- [ ] **Started with search summary** (e.g., "🔎 7 リポジトリ、195 スキルから検索しました")
- [ ] Included skill table with results (from ALL sources)
- [ ] Included **source breakdown table** showing count per source
- [ ] Showed `lastUpdated` date from index
- [ ] Added numbered action menu (NOT command examples)
- [ ] Included web search option with GitHub link ready to open
- [ ] Asked user to choose by number or skill name

### Search Summary Format

**ALWAYS start search responses with this format:**

```
🔎 {N} リポジトリ、{M} スキルから検索しました（最終更新: {date}）
```

**Values are dynamic:**

- `{N}` = count of sources in skill-index.json
- `{M}` = count of skills in skill-index.json
- `{date}` = `lastUpdated` field from skill-index.json

### Output Format

**Trust Level Indicators (MANDATORY):**

Always include trust level badge based on source `type` in skill-index.json:

| Type           | Badge           | Description                              |
| -------------- | --------------- | ---------------------------------------- |
| `official`     | 🏢 **Official** | Anthropic / GitHub 公式リポジトリ        |
| `awesome-list` | 📋 **Curated**  | キュレーションリスト（品質レビュー済み） |
| `community`    | 👥 Community    | コミュニティ製（自己責任で使用）         |

**⚠️ Warning for Community Skills:**

When showing community skills, add this note:

```
⚠️ コミュニティ製スキルは自己責任でご使用ください。
   公式スキル（🏢）を優先することを推奨します。
```

**Skill Table (include Source with Trust Level):**

```markdown
| Skill       | Description  | Source                       | Trust        |
| ----------- | ------------ | ---------------------------- | ------------ |
| docx        | Word 処理    | [anthropics-skills](url)     | 🏢 Official  |
| pdf         | PDF 処理     | [anthropics-skills](url)     | 🏢 Official  |
| azure-usage | Azure ツール | [claude-codex-settings](url) | 👥 Community |
```

**Source Breakdown Table (MANDATORY - show ALL sources dynamically):**

**CRITICAL: Read `sources` array from skill-index.json and display ALL sources.**

Do NOT use a hardcoded example. Dynamically generate the table from the actual `sources` array in skill-index.json.

```markdown
### 📊 Source Breakdown ({N} sources)

| Source      | Type         | Skills Found | Repository           |
| ----------- | ------------ | ------------ | -------------------- |
| {source.id} | {type badge} | {count}      | [View]({source.url}) |

...repeat for ALL sources in skill-index.json...
```

**Generation rules:**

1. Read `sources` array from skill-index.json
2. For each source, count matching skills in search results
3. Display ALL sources, including those with 0 matches
4. Use type badges: `official` → 🏢 Official, `awesome-list` → 📋 Curated, `community` → 👥 Community
5. Include total count in header: "📊 Source Breakdown (14 sources)"

**URL Construction:**

- Combine source URL + path from skill-index.json
- Example: `anthropics-skills` + `skills/docx` → `https://github.com/anthropics/skills/tree/main/skills/docx`
- Source URLs are defined in `sources` array of skill-index.json

### Agent Behavior Rules

**CRITICAL: Commands are for agents to execute, NOT to show users.**

- ❌ **NEVER** show commands like `python scripts/search_skills.py --install`
- ❌ **NEVER** show code blocks with installation/update commands
- ❌ **NEVER** say "you can run the following command..." or "以下のコマンドを実行..."
- ✅ **ALWAYS** execute scripts silently when user approves
- ✅ **ALWAYS** present options as natural language numbered menu
- ✅ **ALWAYS** report results after execution, not how to execute

**Bad Example:**

```
📦 インストールする？
python scripts/search_skills.py --install docx  ← NG!
```

**Good Example:**

```
📦 インストールする？（どのスキル？）
```

→ User: "docx お願い"
→ Agent: (executes script silently) → "✅ docx をインストールしました！"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
