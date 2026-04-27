---
name: doc-confirm
description: Document generation confirmation flow. Shows preview of documents to be generated and asks for user confirmation before proceeding. Used by /init and brainstorming skills. Use when this capability is needed.
metadata:
  author: tygwan
---

# Document Generation Confirmation

## Purpose

아이디어가 정리되면 문서 생성 전에 사용자 확인을 받는 플로우입니다.
이를 통해 사용자가 생성될 문서를 미리 확인하고 진행 여부를 결정할 수 있습니다.

## Trigger Points

1. `/init --full` - Discovery 완료 후
2. `brainstorming` - 디자인 검증 완료 후
3. 수동 호출 - `/doc-confirm` 직접 실행

## Flow

```
[아이디어 정리 완료]
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│                   📋 Document Generation Preview                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  📌 Project Summary                                              │
│  ────────────────────────────────────────────────────────       │
│  Name: {project_name}                                           │
│  Type: {project_type}                                           │
│  Complexity: {LOW|MEDIUM|HIGH}                                  │
│                                                                  │
│  📄 Documents to Generate                                        │
│  ────────────────────────────────────────────────────────       │
│                                                                  │
│  ✓ docs/PRD.md                                                  │
│    • User Stories: ~{count}                                     │
│    • Core Features: {features}                                  │
│    • Requirements: ~{count}                                     │
│                                                                  │
│  ✓ docs/TECH-SPEC.md                                            │
│    • Architecture: {architecture_type}                          │
│    • Tech Stack: {tech_stack}                                   │
│    • Components: ~{count}                                       │
│                                                                  │
│  ✓ docs/PROGRESS.md                                             │
│    • Phases: {phase_count}                                      │
│    • Tasks: ~{task_count}                                       │
│                                                                  │
│  ✓ docs/CONTEXT.md                                              │
│    • AI context optimization                                    │
│                                                                  │
│  📁 Additional (if HIGH complexity)                              │
│  ────────────────────────────────────────────────────────       │
│  ✓ docs/phases/phase-1/                                         │
│    └── SPEC.md, TASKS.md, CHECKLIST.md                         │
│  ... (total {phase_count} phases)                               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## User Interaction

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                  │
│  위 내용으로 문서를 생성하시겠습니까?                               │
│                                                                  │
│  [ ✅ Submit ] - 문서 생성 진행                                   │
│  [ ✏️ Edit ]   - DISCOVERY.md 수정 후 다시 확인                   │
│  [ ❌ Cancel ] - 취소 (DISCOVERY.md만 저장)                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Actions by Choice

### Submit
```yaml
Action: Proceed with document generation
Steps:
  1. Trigger dev-docs-writer agent
  2. Generate: PRD.md, TECH-SPEC.md, PROGRESS.md, CONTEXT.md
  3. If HIGH complexity → Trigger doc-splitter → Phase structure
  4. Show completion summary
```

### Edit
```yaml
Action: Allow user to modify discovery
Steps:
  1. Open/show DISCOVERY.md for editing
  2. Wait for user to confirm edits
  3. Re-run preview with updated content
  4. Show confirmation again
```

### Cancel
```yaml
Action: Save discovery only
Steps:
  1. Ensure DISCOVERY.md is saved
  2. Show message: "Discovery saved. Run '/init --generate' later to create documents."
  3. Exit without generating documents
```

## Preview Generation Logic

```yaml
Extract from DISCOVERY.md:
  project_name: From "# Project Name" or first heading
  project_type: From "Type:" field
  complexity: From "Complexity:" field or auto-detect

  features:
    - Parse "## Core Features" section
    - Count bullet points

  tech_stack:
    - Parse "## Tech Stack" section
    - Extract languages, frameworks, tools

  phases:
    - If complexity = HIGH: estimate 5-8 phases
    - If complexity = MEDIUM: estimate 3-5 phases
    - If complexity = LOW: estimate 1-3 phases

  tasks:
    - Estimate: phases × 5-10 tasks per phase
```

## Integration

### With /init --full

```
/init --full
    │
    ├── Framework Setup
    │
    ├── project-discovery → DISCOVERY.md
    │
    ├── [NEW] doc-confirm (this skill)
    │       │
    │       ├── Submit → Continue to document generation
    │       ├── Edit → Back to discovery refinement
    │       └── Cancel → Stop (DISCOVERY.md saved)
    │
    └── dev-docs-writer → Documents
```

### With brainstorming

```
brainstorming
    │
    ├── Understanding the idea (Q&A)
    │
    ├── Exploring approaches
    │
    ├── Presenting the design (incremental)
    │
    ├── Design validated → Save draft
    │
    ├── [NEW] doc-confirm (this skill)
    │       │
    │       ├── Submit → dev-docs-writer → Full documents
    │       ├── Edit → Back to design refinement
    │       └── Cancel → Keep design doc only
    │
    └── (Optional) Implementation setup
```

## Output Format

### Preview Template

```markdown
## 📋 Document Generation Preview

### Project Summary
- **Name**: {name}
- **Type**: {type}
- **Complexity**: {complexity}
- **Tech Stack**: {stack}

### Documents to Generate

| Document | Content |
|----------|---------|
| `docs/PRD.md` | User stories, features, requirements |
| `docs/TECH-SPEC.md` | Architecture, components, APIs |
| `docs/PROGRESS.md` | Phase tracking, milestones |
| `docs/CONTEXT.md` | AI context optimization |

### Estimated Structure
- **Phases**: {phase_count}
- **Tasks**: ~{task_count}
- **Complexity Level**: {complexity}

---

**위 내용으로 문서를 생성하시겠습니까?**

1. ✅ **Submit** - 문서 생성 진행
2. ✏️ **Edit** - DISCOVERY.md 수정 후 다시 확인
3. ❌ **Cancel** - 취소 (Discovery만 저장)
```

## Usage Examples

### Direct Usage
```bash
# 기존 DISCOVERY.md를 기반으로 확인
/doc-confirm

# 특정 파일 지정
/doc-confirm --source docs/DISCOVERY.md
```

### Integrated Usage (Automatic)
```bash
# /init --full 과정에서 자동 실행
/init --full
# ... discovery ...
# [자동] doc-confirm 실행
# ... user confirms ...
# [자동] document generation ...

# brainstorming 과정에서 자동 실행
/brainstorming "새로운 기능 아이디어"
# ... Q&A, design ...
# [자동] doc-confirm 실행
# ... user confirms ...
# [자동] document generation ...
```

## Key Principles

1. **Never Skip Confirmation**: 아이디어가 정리되면 항상 확인 단계 거침
2. **Clear Preview**: 생성될 문서를 명확히 미리 보여줌
3. **User Control**: 사용자가 진행/수정/취소 결정
4. **Preserve Work**: Cancel해도 DISCOVERY.md는 저장됨
5. **Iterative**: Edit 선택 시 무한 반복 가능

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
