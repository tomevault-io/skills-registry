---
name: validate
description: cc-initializer 설정 및 구성 검증. settings.json, hooks, agents, skills, documents 무결성 확인. Use when this capability is needed.
metadata:
  author: tygwan
---

# /validate - 설정 검증 스킬

## Usage

```bash
/validate [mode] [options]
```

### Modes

| Mode | Description |
|------|-------------|
| `--quick` | settings.json만 검증 (기본) |
| `--full` | 모든 구성요소 검증 |
| `--fix` | 자동 수정 시도 |
| `--report` | 상세 보고서 생성 |

## Validation Workflow

```
┌────────────────────────────────────────────────────────────────────┐
│                      /validate WORKFLOW                             │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. settings.json 검증                                              │
│     ├── JSON 문법 검사                                              │
│     ├── 필수 섹션 확인                                              │
│     └── Hook 참조 유효성                                            │
│                                                                     │
│  2. Hooks 검증                                                      │
│     ├── 파일 존재 확인                                              │
│     ├── 실행 권한 확인                                              │
│     └── 스크립트 문법 검사                                          │
│                                                                     │
│  3. Agents 검증                                                     │
│     ├── Frontmatter 유효성                                          │
│     ├── 필수 필드 확인                                              │
│     └── 중복 이름 검사                                              │
│                                                                     │
│  4. Skills 검증                                                     │
│     ├── SKILL.md 존재 확인                                          │
│     ├── Frontmatter 유효성                                          │
│     └── 템플릿 파일 확인                                            │
│                                                                     │
│  5. Documents 검증                                                  │
│     ├── 표준 위치 확인                                              │
│     ├── Phase 구조 확인                                             │
│     └── 링크 유효성 검사                                            │
│                                                                     │
│  6. 보고서 생성                                                     │
│     └── 결과 요약 및 권장사항                                       │
│                                                                     │
└────────────────────────────────────────────────────────────────────┘
```

## Validation Checks

### settings.json

```yaml
Required Sections:
  hooks:
    - PreToolUse
    - PostToolUse
    - Notification
  phase:
    - enabled
    - document_structure
  sprint:
    - enabled
    - phase_integration
  documents:
    - standard_locations
  safety:
    - block_dangerous_commands
```

### Hooks

```yaml
Expected Files:
  - pre-tool-use-safety.sh
  - post-tool-use-tracker.sh
  - phase-progress.sh
  - auto-doc-sync.sh
  - notification-handler.sh

Checks:
  - File exists
  - Is executable
  - Has shebang (#!/bin/bash)
  - No syntax errors
```

### Agents

```yaml
Frontmatter Required:
  - name
  - description
  - tools

Optional:
  - model
  - color

Checks:
  - Valid YAML frontmatter
  - Unique names
  - Valid tool references
```

### Skills

```yaml
Structure:
  skill-name/
  ├── SKILL.md (required)
  └── templates/ (optional)

Frontmatter Required:
  - name
  - description
```

### Documents

```yaml
Standard Locations:
  - docs/PROGRESS.md
  - docs/CONTEXT.md
  - docs/PRD.md (optional)
  - docs/TECH-SPEC.md (optional)
  - docs/phases/ (if phase enabled)
  - docs/sprints/ (if sprint enabled)
```

## Output

### Quick Mode

```
🔍 VALIDATE: Quick Check

✅ settings.json: Valid
✅ Required sections: All present
✅ Hook references: All valid

Status: PASS
```

### Full Mode

```
🔍 VALIDATE: Full Validation

📋 Validation Summary
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Component        Status    Issues
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
settings.json    ✅ PASS   0
Hooks (5)        ⚠️ WARN   1
Agents (20)      ✅ PASS   0
Skills (10)      ✅ PASS   0
Commands (6)     ✅ PASS   0
Documents        ⚠️ WARN   2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Details

[Hooks]
✅ pre-tool-use-safety.sh
✅ post-tool-use-tracker.sh
✅ phase-progress.sh
⚠️ auto-doc-sync.sh (not executable)
✅ notification-handler.sh

[Documents]
⚠️ docs/PROGRESS.md (missing)
⚠️ docs/phases/ (empty)

💡 Recommendations

1. Fix hook permissions:
   chmod +x .claude/hooks/auto-doc-sync.sh

2. Initialize project documents:
   /init --docs-only

Overall Status: WARN (3 issues)
```

### Fix Mode

```
🔧 VALIDATE: Fix Mode

Attempting automatic fixes...

[1/3] Hook permissions
   ✅ chmod +x .claude/hooks/auto-doc-sync.sh

[2/3] Missing directories
   ✅ mkdir -p docs/phases

[3/3] Missing files
   ⚠️ docs/PROGRESS.md (run /init to create)

Fixed: 2/3 issues
Manual action required: 1 issue
```

## Integration

### With /init

```bash
# /init automatically runs validation after setup
/init --full
# → runs /validate --quick at end
```

### With quality-gate

```bash
# Pre-commit can include validation
quality-gate pre-commit
# → includes /validate --quick
```

### Session Start

```bash
# Auto-run on session start (if enabled in settings)
# settings.json:
# "validation": { "auto_check_on_start": true }
```

## Configuration

```json
// settings.json
{
  "validation": {
    "auto_check_on_start": false,
    "strict_mode": false,
    "ignore_patterns": [
      "*.backup",
      "*.tmp"
    ],
    "required_agents": [
      "progress-tracker",
      "phase-tracker"
    ],
    "required_hooks": [
      "phase-progress.sh"
    ]
  }
}
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | All validations passed |
| 1 | Warnings found |
| 2 | Errors found |
| 3 | Critical errors (invalid settings.json) |

## Related

| Command | Purpose |
|---------|---------|
| `/init` | Initialize configuration |
| `/agile-sync` | Sync documentation |
| `config-validator` | Validation agent |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
