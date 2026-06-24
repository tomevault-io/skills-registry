---
name: claude-plan
description: Anthropic 風格的專業計畫管理 Skill - 自然語言驅動、預設並行、全自動 GitHub 整合 Use when this capability is needed.
metadata:
  author: miles990
---

# Claude Plan v1.0.0

> 自然語言描述 → **自動分解** → **預設並行** → PDCA 執行 → **全自動 GitHub** → Dashboard 追蹤 → 支持中斷續做

## 快速導覽

| 模組 | 用途 | 路徑 |
|------|------|------|
| **00-quickstart** | 快速開始 | [→](./00-quickstart/) |
| **01-planning** | 計畫制定（自然語言解析 + 任務分解） | [→](./01-planning/) |
| **02-tracking** | 進度追蹤（Git + Dashboard 混合） | [→](./02-tracking/) |
| **03-execution** | PDCA 執行循環 | [→](./03-execution/) |
| **04-interruption** | 中斷/恢復/智能插入 | [→](./04-interruption/) |
| **05-github** | GitHub 全自動整合 | [→](./05-github/) |
| **06-parallel** | 多 Session 預設並行 | [→](./06-parallel/) |

## 使用方式

```bash
/claude-plan [計畫描述]

# 範例
/claude-plan 建立電商網站 MVP
/claude-plan 重構認證系統，支援 OAuth 2.0
/claude-plan 撰寫 API 文檔並建立測試覆蓋率 80%
```

### Flags

```bash
--dashboard        # 開啟視覺化 Dashboard
--add "需求"       # 智能插入新需求
--resume           # 從中斷處繼續（自動偵測）
--list             # 列出所有進行中的計畫
--switch "專案"    # 切換到指定專案
```

## 核心設計

### 基於 Anthropic 最佳實踐

| 來源 | 實踐 | 本 Skill 整合 |
|------|------|---------------|
| Boris Cherny Tips | 平行化策略（5-10 Claude 實例） | 預設並行，多 Session 自動認領 |
| Boris Cherny Tips | 驗證驅動（品質提升 2-3 倍） | PDCA Check 強制驗證 |
| Boris Cherny Tips | Subagents 策略 | 整合 verify-app, build-validator |
| Claude Code Docs | 自然語言命令 | 口語描述 → 自動分解任務 |
| Claude Code Docs | MCP 整合 | 連接 spec-workflow Dashboard |

### 設計原則

| 原則 | 說明 |
|------|------|
| **預設並行** | 任務自動分解為可並行單元，新 Session 自動 pick up |
| **全自動 GitHub** | commit → push → PR 無需干預 |
| **智能優先級** | AI 評估新需求緊急程度，自動排序 |
| **中斷友好** | 任何時刻中斷，下次自動從未完成處繼續 |

## 執行流程

```
/claude-plan 建立電商 MVP
         ↓
┌─────────────────────────────────────────────────────────┐
│  Phase 1: 自然語言解析                                   │
│  • 理解需求意圖                                          │
│  • 識別子目標                                            │
│  • 定義完成標準                                          │
└─────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────┐
│  Phase 2: 任務自動分解                                   │
│  • 分解為可並行單元                                      │
│  • 標記依賴關係                                          │
│  • 估算複雜度                                            │
└─────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────┐
│  Phase 3: 並行就緒                                       │
│  ┌─────────┬─────────┬─────────┐                        │
│  │ Task A  │ Task B  │ Task C  │  ← 可並行              │
│  │ [待認領] │ [待認領] │ [待認領] │                        │
│  └─────────┴─────────┴─────────┘                        │
│           ↓                                              │
│  ┌─────────┐                                             │
│  │ Task D  │  ← 依賴 A+B                                │
│  │ [阻塞中] │                                             │
│  └─────────┘                                             │
└─────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────┐
│  Phase 4: PDCA 執行（每個 Task）                         │
│  Plan → Do → Check → Act                                │
│  驗證通過 → 自動 commit + push                           │
└─────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────┐
│  Phase 5: 全自動 GitHub                                  │
│  • 每個 Task 完成 → commit                               │
│  • Milestone 完成 → push + PR                            │
└─────────────────────────────────────────────────────────┘
```

## 狀態存儲

### 混合模式架構

```
.claude/plans/                    # Git-tracked（主計畫）
├── index.json                    # 所有計畫索引
└── ecommerce-mvp/
    ├── plan.md                   # 主計畫文件
    ├── north-star.md             # 北極星錨定
    ├── sessions/
    │   ├── session-001.json      # Session 狀態快照
    │   └── session-002.json
    └── tasks/
        ├── backend-api.md        # 子任務詳情
        └── frontend-ui.md

specs/ecommerce-mvp/              # spec-workflow 整合（Dashboard）
├── requirements.md
├── design.md
├── tasks.md
└── implementation-log/
```

## 多 Session 並行

### 運作機制

```
新 Session 啟動: /claude-plan
         ↓
┌─────────────────────────────────────────────────────────┐
│  1. 讀取 .claude/plans/index.json                        │
│  2. 檢查可認領的任務（無鎖 + 依賴已完成）                  │
│  3. 自動認領第一個可用任務                                │
│  4. 創建任務鎖（防止其他 Session 重複認領）                │
│  5. 執行 PDCA                                            │
│  6. 完成後釋放鎖 + 更新狀態                               │
└─────────────────────────────────────────────────────────┘
```

### 任務鎖機制

```json
{
  "task_id": "backend-api",
  "locked_by": "session-001",
  "locked_at": "2025-01-21T10:30:00Z",
  "expires_at": "2025-01-21T11:30:00Z"
}
```

## Dashboard 整合

```bash
/claude-plan --dashboard
```

**功能**：
- 視覺化進度（spec-workflow Dashboard）
- 即時 Session 狀態
- 任務依賴圖
- GitHub 活動追蹤

## 智能需求插入

```bash
/claude-plan --add "新增支付功能"
```

**AI 評估流程**：
1. 分析新需求內容
2. 評估緊急程度（P0-P3）
3. 檢查與現有任務的依賴
4. 自動決定：插隊 or 排隊
5. 更新計畫 + 通知相關 Session

## 中斷與恢復

### 自動狀態保存

每次中斷時自動保存：
- 當前任務進度
- 已完成的驗證
- 未提交的變更

### 恢復機制

```bash
/claude-plan --resume
# 或直接
/claude-plan  # 自動偵測未完成計畫
```

## 完成信號

- `✅ PLAN COMPLETED: [計畫名稱]`
- `⏸️ NEED HUMAN: [原因]`
- `🔄 TASKS REMAINING: X/Y`

## 相關資源

- [Boris Cherny Claude Code Tips](../.claude/memory/learnings/2025-01-07-boris-cherny-claude-code-tips.md)
- [spec-workflow 整合](./05-integration/_base/spec-workflow.md)
- [Anthropic Claude Code 官方文檔](https://code.claude.com/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miles990) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
