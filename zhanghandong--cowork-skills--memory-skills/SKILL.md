---
name: memory-skills
description: | Use when this capability is needed.
metadata:
  author: zhanghandong
---

# Memory Skills (CoALA-based)

> **Memory = Filesystem** - Cognitive architecture for language agents

Based on the [CoALA framework](https://arxiv.org/abs/2309.02427) (Cognitive Architectures for Language Agents), this skill implements a complete memory system with four memory types.

## Memory Architecture

```
                    ┌─────────────────┐
                    │  Working Memory │  ← Short-term, central hub
                    └────────┬────────┘
                             │
        ┌────────────────────┼────────────────────┐
        ↓                    ↓                    ↓
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│   Episodic    │   │   Semantic    │   │  Procedural   │
│   情景记忆     │   │   语义记忆     │   │   程序性记忆   │
└───────────────┘   └───────────────┘   └───────────────┘
        ↑                                        ↑
    Long-term ─────────────────────────────── Long-term
```

## Memory Types (CoALA)

| Type | Purpose | Example |
|------|---------|---------|
| **Working** | Current session context | Active goals, intermediate reasoning |
| **Episodic** | Past experiences | Mistakes, solutions, conversation history |
| **Semantic** | World knowledge | Facts, learnings, reflections |
| **Procedural** | How to do things | Preferences, workflows, prompt templates |

## Directory Structure

### Global Memory (`~/.claude/memory/`)

Cross-project shared knowledge:

```
~/.claude/memory/
├── working/                    # Working Memory (可持久化的会话上下文)
│   └── {name}-context.md       # 保存的会话状态 (通过 /save-context)
│
├── procedural/                 # Procedural Memory
│   ├── preferences.md          # User preferences
│   ├── workflows.md            # Common workflows
│   └── prompts/                # Reusable prompt templates
│       └── {name}.md
│
├── semantic/                   # Semantic Memory
│   ├── learnings/              # Knowledge by topic
│   │   ├── rust-tips.md
│   │   ├── git-tips.md
│   │   └── {topic}.md
│   └── reflections/            # Insights from reflection
│       └── {date}-{insight}.md
│
├── episodic/                   # Episodic Memory
│   ├── mistakes/               # Errors and how they were fixed
│   │   └── {date}-{error}.md
│   ├── solutions/              # Successfully solved problems
│   │   └── {date}-{problem}.md
│   └── trajectories/           # Complete task execution logs
│       └── {date}-{task}.md
│
└── index.json                  # Search index (auto-generated)
```

### Project Memory (`.claude/memory/`)

Project-specific knowledge:

```
{project}/.claude/memory/
├── semantic/
│   ├── architecture.md         # Project architecture
│   ├── conventions.md          # Coding conventions
│   └── decisions.md            # Technical decisions (ADRs)
│
├── episodic/
│   └── sessions/               # Session summaries
│       └── {date}-{topic}.md
│
└── index.json                  # Project search index
```

## Commands

| Command | Action | Memory Type |
|---------|--------|-------------|
| `/remember <content>` | Save to appropriate memory | Auto-detect |
| `/recall <topic>` | Search and retrieve | All types |
| `/forget <topic>` | Delete specific memory | All types |
| `/reflect` | Generate insights from episodic → semantic | Episodic → Semantic |
| `/summarize-session` | Save current session summary | Episodic |
| `/save-context` | Save key context for later resume | Working → File |
| `/load-context` | Load saved context into session | File → Working |

### Working Memory Commands

**`/save-context [name]`** - 保存当前会话的关键上下文

触发词: "保存上下文", "save context", "保存当前状态"

```markdown
# 保存到: ~/.claude/memory/working/{name}-context.md 或 .claude/memory/working/

## Session Context: {name}
**Saved**: {timestamp}

### Current Goals
- {what we're trying to accomplish}

### Key Decisions Made
- {important decisions in this session}

### Important Context
- {critical information to remember}

### Next Steps
- {what to do when resuming}
```

**`/load-context [name]`** - 加载之前保存的上下文

触发词: "加载上下文", "load context", "恢复上下文", "继续之前的工作"

1. Search for context files in working/ directory
2. Display available contexts if name not specified
3. Load and summarize the selected context
4. Confirm: "已加载上下文: {name}"

## Instructions for Claude

### Action Space (CoALA)

You have four memory actions:

1. **Retrieval** (读取): Load from long-term → working memory
2. **Reasoning** (推理): Process in working memory
3. **Learning** (写入): Save from working → long-term memory
4. **Forgetting** (遗忘): Remove from long-term memory

### When User Says "Remember" / "记住"

1. **Detect memory type**:
   ```
   Content about...        → Memory Type      → Location
   ─────────────────────────────────────────────────────
   User preference         → Procedural       → procedural/preferences.md
   Workflow/process        → Procedural       → procedural/workflows.md
   Fact/knowledge          → Semantic         → semantic/learnings/{topic}.md
   Project architecture    → Semantic         → .claude/memory/semantic/architecture.md
   Error encountered       → Episodic         → episodic/mistakes/{date}-{error}.md
   Problem solved          → Episodic         → episodic/solutions/{date}-{problem}.md
   ```

2. **Format content**:
   ```markdown
   ## {YYYY-MM-DD} - {brief title}

   {content}

   **Context**: {why this is important}
   **Tags**: {relevant keywords}

   ---
   ```

3. **Update index** (if index.json exists):
   ```json
   {
     "entries": [
       {
         "file": "semantic/learnings/rust-tips.md",
         "keywords": ["rust", "ownership", "borrow"],
         "updated": "2026-01-25"
       }
     ]
   }
   ```

4. **Confirm**: Tell user what was saved, where, and memory type

### When User Says "Recall" / "回忆"

1. **Search priority**:
   - First: Project memory `.claude/memory/`
   - Then: Global memory `~/.claude/memory/`
   - Check: `index.json` for keyword matches
   - Fallback: Scan file names and content

2. **Return results with metadata**:
   ```
   Found 2 relevant memories:

   📁 [Semantic] ~/.claude/memory/semantic/learnings/rust-tips.md
      → "Use Arc<Mutex<T>> for shared mutable state..."

   📁 [Episodic] .claude/memory/episodic/solutions/2026-01-20-async-bug.md
      → "Fixed by adding .await after spawn..."
   ```

3. **If nothing found**:
   ```
   没有找到关于 "{topic}" 的记忆。
   → 使用 /remember 来保存新知识
   ```

### When User Says "Forget" / "忘记"

1. **Confirm before deletion**:
   ```
   找到以下相关记忆:
   - ~/.claude/memory/semantic/learnings/old-api.md

   确认删除吗? (y/n)
   ```

2. **Delete and update index**

3. **Confirm**: "已删除关于 {topic} 的记忆"

### When User Says "Reflect" / "反思"

Reflection converts episodic memories into semantic knowledge:

1. **Scan recent episodic memories** (last 7 days)

2. **Identify patterns**:
   - Repeated mistakes → Learning opportunity
   - Multiple solutions → Best practice
   - Common trajectories → Workflow template

3. **Generate reflection**:
   ```markdown
   ## 2026-01-25 - Reflection: Async Error Handling

   **Pattern observed**: In 3 recent sessions, async errors were caused by missing .await

   **Insight**: Always check for missing .await when debugging async code

   **Source episodes**:
   - episodic/mistakes/2026-01-20-async-bug.md
   - episodic/mistakes/2026-01-22-tokio-error.md
   - episodic/solutions/2026-01-23-fixed-spawn.md

   ---
   ```

4. **Save to**: `semantic/reflections/{date}-{insight}.md`

### When User Says "Summarize Session" / "总结会话"

1. **Gather session context**:
   - What was accomplished
   - Key decisions made
   - Problems encountered and solutions
   - New learnings

2. **Generate summary**:
   ```markdown
   ## 2026-01-25 - Session: Implementing Memory System

   **Goals**: Improve memory-filesystem skill based on CoALA

   **Accomplished**:
   - Added working memory support
   - Implemented forgetting mechanism
   - Added reflection capability

   **Key Decisions**:
   - Used JSON for search index
   - Separated global vs project memory

   **Learnings**:
   - CoALA has 4 memory types, not 3

   ---
   ```

3. **Save to**:
   - Project: `.claude/memory/episodic/sessions/{date}-{topic}.md`
   - Or global: `~/.claude/memory/episodic/trajectories/{date}-{task}.md`

### Auto-Learning (Proactive)

When you discover valuable information during a session:

1. **Detect learning opportunity**:
   - Error fixed → Potential mistake memory
   - New technique used → Potential learning
   - Repeated action → Potential workflow

2. **Ask user**: "发现了一个有用的技巧，要我记住吗？"

3. **If confirmed**: Save to appropriate memory location

## Quick Reference

| Trigger | Memory Type | Location |
|---------|-------------|----------|
| "保存上下文" / "save context" | Working | `working/{name}-context.md` |
| "加载上下文" / "load context" | Working | Read from `working/` |
| "记住这个偏好" | Procedural | `procedural/preferences.md` |
| "记住这个流程" | Procedural | `procedural/workflows.md` |
| "记住这个技巧" | Semantic | `semantic/learnings/{topic}.md` |
| "记住项目架构" | Semantic | `.claude/memory/semantic/architecture.md` |
| "记住这个错误" | Episodic | `episodic/mistakes/{date}-{error}.md` |
| "记住这个解决方案" | Episodic | `episodic/solutions/{date}-{problem}.md` |
| "回忆/你知道..." | Retrieval | Search all locations |
| "忘记..." | Forgetting | Delete from memory |
| "反思" | Reflection | Episodic → Semantic |
| "总结会话" | Learning | Save session to episodic |

## Example Interactions

### Example 1: Remember
```
User: "记住：Rust 中使用 thiserror 来定义错误类型"

Claude:
1. Detect: Knowledge/fact → Semantic memory
2. Location: ~/.claude/memory/semantic/learnings/rust-tips.md
3. Append with date, tags
4. Update index.json
5. Response: "✓ 已保存到语义记忆 (rust-tips.md)"
```

### Example 2: Reflect
```
User: "/reflect"

Claude:
1. Scan episodic/mistakes/ and episodic/solutions/ (last 7 days)
2. Found pattern: 3 instances of borrow checker errors
3. Generate insight: "Common cause: trying to move value after borrow"
4. Save to: semantic/reflections/2026-01-25-borrow-patterns.md
5. Response: "✓ 生成了 1 条反思，已保存到语义记忆"
```

### Example 3: Forget
```
User: "忘记关于旧 API 的记忆"

Claude:
1. Search: Found semantic/learnings/old-api-v1.md
2. Confirm: "找到 1 条相关记忆，确认删除?"
3. User confirms
4. Delete file, update index
5. Response: "✓ 已删除关于旧 API 的记忆"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhanghandong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
