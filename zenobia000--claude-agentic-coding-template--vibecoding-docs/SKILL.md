---
name: vibecoding-docs
description: 📄 VibeCoding Document Generator - 根據七問答案自動填充 VibeCoding 範本產出專案文檔 Use when this capability is needed.
metadata:
  author: zenobia000
---

# 📄 VibeCoding Document Generator Skill

此技能將七問收集的需求資訊**自動填充**到 VibeCoding 範本中，產出完整的專案文檔。

## 🎯 觸發條件

當以下情況發生時，此技能**自動觸發**：

1. **`/task-init` 七問完成後** - 自動生成初始文檔
2. **`/generate-docs` 命令執行時** - 手動觸發文檔生成
3. **專案需求變更時** - 更新現有文檔

## 📋 七問→範本映射

### 完整映射關係

```
┌─────────────────────────────────────────────────────────────────┐
│  七問輸入                    VibeCoding 範本       輸出文檔     │
├─────────────────────────────────────────────────────────────────┤
│  問題 1: 核心問題     ──→   02_project_brief   ──→  01_PRD.md  │
│  問題 2: 核心功能     ──→   02_project_brief   ──→  01_PRD.md  │
│                       ──→   07_module_spec     ──→  05_Modules.md│
│  問題 3: 技術偏好     ──→   05_architecture    ──→  02_Arch.md │
│                       ──→   04_adr_template    ──→  ADRs/       │
│  問題 4: 用戶體驗     ──→   17_frontend_ia     ──→  03_UX.md   │
│  問題 5: 規模性能     ──→   05_architecture    ──→  02_Arch.md │
│  問題 6: 時程資源     ──→   16_wbs_plan        ──→  04_WBS.md  │
│  問題 7: 成功標準     ──→   02_project_brief   ──→  01_PRD.md  │
└─────────────────────────────────────────────────────────────────┘
```

### 範本欄位對應

詳見 `mappings/question-to-template.md`

## 🔧 執行流程

### Phase 1: 資料收集

```
📥 讀取七問答案來源（優先順序）：
1. .claude/taskmaster-data/project.json
2. 當前對話上下文
3. 請求用戶補充
```

### Phase 2: 範本載入

```
📂 載入 VibeCoding 範本：
VibeCoding_Workflow_Templates/
├── 02_project_brief_and_prd.md
├── 04_architecture_decision_record_template.md
├── 05_architecture_and_design_document.md
├── 06_api_design_specification.md
├── 07_module_specification_and_tests.md
├── 16_wbs_development_plan_template.md
└── 17_frontend_information_architecture_template.md
```

### Phase 3: 智能填充

```
🤖 填充策略：
1. 解析範本結構（識別可填充區域）
2. 映射七問答案到對應欄位
3. 生成補充內容（如 User Stories）
4. 驗證完整性
```

### Phase 4: 輸出文檔

```
📤 輸出到 docs/ 目錄：
docs/
├── 01_PRD.md
├── 02_Architecture.md
├── 03_UX_Design.md
├── 04_WBS.md
├── 05_Module_Spec.md
├── 06_API_Spec.md
└── ADRs/
    └── 001_tech_stack_decision.md
```

## 📊 文檔模板

### 01_PRD.md 結構

```markdown
# [專案名稱] - 產品需求文檔

## Executive Summary
[← 問題 1 + 問題 2 摘要]

## Problem Statement
[← 問題 1: 核心問題]

### Current Pain Points
[← 問題 1: 目前的痛點]

### Why Existing Solutions Fail
[← 問題 1: 現有方案無法滿足]

## Target Users
[← 問題 4: 主要用戶群體]

## Core Features
[← 問題 2: 3-5個重要功能]

### Feature 1: [功能名稱]
- Description: [描述]
- User Story: As a [用戶], I want to [功能], so that [價值]
- Acceptance Criteria: [驗收標準]

## Success Metrics
[← 問題 7: 成功標準]

### Technical Metrics
[← 問題 7: 技術指標]

### Business Metrics
[← 問題 7: 業務指標]

## Constraints & Assumptions
[← 問題 3: 技術限制]
[← 問題 6: 時程限制]

## Out of Scope
[根據範圍推斷]
```

### 02_Architecture.md 結構

```markdown
# [專案名稱] - 系統架構文檔

## Overview
[← 問題 1 + 問題 2]

## Tech Stack Decision
[← 問題 3: 技術偏好]

### Language & Framework
[← 問題 3: 偏好的技術棧]

### Database
[← 問題 3: 資料庫選擇]

### Deployment Environment
[← 問題 3: 部署環境]

## System Components
[← 問題 2: 核心功能分解]

## Non-Functional Requirements
[← 問題 5: 規模和性能要求]

### Performance
- Response Time: [← 問題 5: 響應時間]
- Throughput: [← 問題 5: 吞吐量]

### Scalability
- Concurrent Users: [← 問題 5: 同時在線]
- Data Volume: [← 問題 5: 數據量級]

## Integration Points
[← 問題 3: 必須整合的系統]
```

## 🎨 品質檢查

### 完整性驗證

```
📊 文檔完整性檢查：

欄位覆蓋率:
├── 01_PRD.md        [████████░░] 80%
├── 02_Architecture  [██████████] 100%
├── 03_UX_Design     [██████░░░░] 60%
└── 04_WBS           [████████░░] 80%

缺失欄位:
🔴 01_PRD.md: User Stories (2/5)
🟡 03_UX_Design: User Journey Map
```

### Traffic Light 狀態

| 覆蓋率 | 狀態 | 說明 |
|--------|------|------|
| ≥90% | 🟢 | 可直接進入開發 |
| 70-89% | 🟡 | 建議補充後開發 |
| <70% | 🔴 | 必須補充關鍵資訊 |

## 🔗 與 TaskMaster 整合

### 自動同步到 WBS

文檔生成後，自動提取任務到 WBS：

```
docs/04_WBS.md → .claude/taskmaster-data/wbs-todos.json
```

### Context 輸出

報告輸出位置: `.claude/context/docs/`

## 📚 參考資源

- `mappings/question-to-template.md` - 完整欄位映射
- `templates/` - 輸出文檔範本
- `scripts/validate-docs.sh` - 完整性驗證腳本

---

**將需求轉化為文檔，讓開發有跡可循！** 📄→💻

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zenobia000) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
