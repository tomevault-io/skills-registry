---
name: jira-workflow-agent
description: JIRA 티켓 기반 전체 개발 파이프라인 자동화. 티켓 분석→구현→빌드→PR까지 semi-auto로 실행. "jira", "ticket", "workflow", "pipeline" 키워드 시 활성화 Use when this capability is needed.
metadata:
  author: jayleekr
---

# JIRA Workflow Agent

JIRA 티켓 URL부터 Pull Request 생성까지 전체 개발 파이프라인을 자동화하는 스킬.

## When to Use This Skill

이 스킬은 다음 요청에서 활성화됩니다:
- JIRA 티켓 URL 제공 (`https://sonatus.atlassian.net/browse/CCU2-XXXXX`)
- "JIRA 티켓으로 작업 시작" 요청
- "티켓 기반 자동 구현" 요청
- 키워드: "jira workflow", "ticket automation", "auto implement"

## Prerequisites

### Environment Setup
1. **JIRA 인증**: `.env` 파일에 API Token 설정
   ```bash
   JIRA_BASE_URL=https://sonatus.atlassian.net/
   JIRA_EMAIL=your.email@sonatus.com
   JIRA_API_TOKEN=your_api_token
   ```

2. **GitHub CLI**: `gh` 설치 및 인증 완료
   ```bash
   gh auth status
   ```

3. **Git Repository**: 초기화된 git 저장소

### Required Tools
- `curl`: JIRA API 호출
- `jq`: JSON 파싱
- `git`: 버전 관리
- `gh`: GitHub PR 생성

## Core Workflow

### 1. Understand User Intent

사용자 요청에서 다음 정보를 파악:
- **JIRA Ticket URL** 또는 **Ticket ID**: `CCU2-XXXXX` 형식
- **작업 범위**: 전체 파이프라인 또는 특정 모드만 실행
- **승인 모드**: Semi-auto (기본값) 또는 Full-auto

필요시 질문:
- "JIRA 티켓 URL을 제공해주세요"
- "전체 파이프라인을 실행할까요, 아니면 특정 단계만 실행할까요?"

### 2. Execute Appropriate Mode

#### Mode 1: ANALYZE (자동)
티켓 분석 및 실행 계획 생성.

**실행 단계**:
1. JIRA 티켓 데이터 fetch
2. 작업 유형 분류 (feature/bugfix/refactor)
3. 요구사항 파싱
4. 영향받는 파일/컴포넌트 식별
5. 실행 계획 생성
6. Serena memory 저장
7. TodoWrite로 체크박스 생성

**출력**:
```markdown
## 📋 Execution Plan

**JIRA**: CCU2-17741 - Add config parameter for daemon startup
**Work Type**: Feature
**Priority**: High
**Complexity**: Medium

### Affected Files:
- src/daemon/main.cpp
- include/config.h

### Implementation Plan:
1. Add CONFIG_STARTUP_DELAY to config.h
2. Update main.cpp to read parameter
3. Add validation logic

### Estimated Effort: 15-20 minutes
```

Reference: `references/jira-analysis.md`

#### Mode 2: IMPLEMENT (승인 필요 ⚠️)
브랜치 생성 및 코드 구현.

**실행 단계**:
1. 실행 계획 불러오기 (from memory)
2. Git 상태 확인
3. Feature 브랜치 생성
4. **[APPROVAL CHECKPOINT 1]** ⚠️
   - 구현 계획 표시
   - 사용자 승인 대기
5. 승인 시: 코드 생성 및 적용
6. 메모리 업데이트

**Approval Checkpoint 1**:
```markdown
## 🔍 Implementation Plan Review

**JIRA**: CCU2-17741
**Branch**: CCU2-17741-add-config-parameter

### Planned Changes:
- Files: src/daemon/main.cpp, include/config.h
- Approach: Add configuration parameter with validation
- Risk: Low (isolated change)

**Proceed with code implementation?**
- approve: Continue with implementation
- modify: Adjust the plan
- reject: Abort workflow
```

Reference: `references/workflow-modes.md`

#### Mode 3: VERIFY (자동)
빌드 및 테스트 실행.

**실행 단계**:
1. 빌드 시스템 감지
2. 빌드 실행 및 로그 캡처
3. 테스트 실행
4. 정적 분석 (MISRA for C/C++)
5. 검증 리포트 생성
6. 메모리 저장

**출력**:
```markdown
## ✅ Verification Report

- Build: PASSED (0 errors, 0 warnings)
- Tests: PASSED (15/15 tests)
- MISRA: PASSED (0 violations)
- Quality: Grade A

Ready for submission.
```

Reference: `references/workflow-modes.md`

#### Mode 4: SUBMIT (승인 필요 ⚠️)
커밋 및 PR 생성.

**실행 단계**:
1. 검증 결과 확인
2. 커밋 메시지 생성
3. `/jira-commit` 실행
4. **[APPROVAL CHECKPOINT 2]** ⚠️
   - PR 상세 정보 표시
   - 사용자 승인 대기
5. 승인 시: `/jira-pr` 실행
6. 최종 메모리 저장

**Approval Checkpoint 2**:
```markdown
## 📤 Pull Request Review

**Branch**: CCU2-17741-add-config-parameter
**Commit**: abc123def

### Verification Results:
✅ All checks passed

### PR Details:
- Title: [CCU2-17741] Add config parameter
- Files: 2 modified (+45/-12 lines)

**Create pull request?**
- approve: Create PR
- modify: Edit PR details
- reject: Keep commits on branch only
```

Reference: `references/approval-checkpoints.md`

#### Mode 5: COMPLETE (오케스트레이터)
전체 파이프라인 실행.

**실행 단계**:
1. Session 초기화 (resume check)
2. Mode 1 (ANALYZE) 실행
3. Mode 2 (IMPLEMENT) 실행 - 승인 필요
4. Mode 3 (VERIFY) 실행
5. Mode 4 (SUBMIT) 실행 - 승인 필요
6. 워크플로우 완료 기록

**Orchestration Logic**:
```
ANALYZE → IMPLEMENT (approval) → VERIFY
                                     ↓
                                  PASSED?
                                   ↓   ↓
                               YES   NO
                                ↓     ↓
                           SUBMIT  ABORT
                         (approval) (with options)
```

Reference: `references/workflow-modes.md`

### 3. Handle Errors Gracefully

#### Error Categories

**Transient Errors** (재시도):
- JIRA API timeout → 3회 재시도 (exponential backoff)
- Network errors → Retry with backoff

**User Errors** (안내):
- Invalid ticket URL → 형식 예시 제공
- Missing credentials → ~/.env 설정 가이드

**State Errors** (롤백):
- Build failure → 로그 분석, 수정 제안, 롤백 옵션
- Test failure → 실패 상세 표시, 수동 수정 옵션

**Rollback Levels**:
- Level 1 (Soft): `git reset --hard HEAD`
- Level 2 (Branch): `git branch -D CCU2-XXXXX-*`
- Level 3 (Memory): `delete_memory(checkpoint_*)`
- Level 4 (Complete): 전체 리셋

Reference: `references/error-recovery.md`

## Tool Integration

### JIRA API
- **Authentication**: Basic Auth (email:token)
- **Endpoint**: `/rest/api/3/issue/{ticket_id}`
- **Data Extraction**: summary, description, status, priority, components

Utility Script: `/Users/jaylee/.claude-config/projects/container-manager/scripts/jira-integration.sh`

### Existing Commands
- **`/jira-commit`**: JIRA-aware Git commit
- **`/jira-pr`**: GitHub PR with JIRA linking

Reference: `references/integration-commands.md`

### Serena Memory
- **Structure**: Hierarchical (plan → phase → task)
- **Persistence**: Cross-session via `write_memory()`, `read_memory()`
- **Checkpointing**: Every 30min or at critical milestones

Reference: `references/memory-schema.md`

### TodoWrite
- **States**: pending, in_progress, completed, blocked
- **Integration**: Real-time progress tracking
- **Lifecycle**: Session start → updates → checkpoint → save

## Communication Patterns

### Progress Display
Use checkboxes for visual progress:
```markdown
## 📋 Workflow Progress

- ✅ Analyze JIRA ticket
- 🔄 Implement code changes (in progress)
- ⏳ Run build & tests (pending)
- ⏳ Create pull request (pending)
```

### User Decisions
Present clear options at approval checkpoints:
```markdown
**What would you like to do?**
- `approve` - Proceed with the plan
- `modify` - Adjust the plan
- `reject` - Abort workflow
```

### Error Messages
Provide actionable guidance:
```markdown
❌ Build failed with 3 errors

### Errors:
1. src/main.cpp:45 - undefined reference to 'foo'

### Suggested Actions:
- Fix errors manually
- Rollback changes (git reset)
- Abort workflow

**What would you like to do?**
```

## Success Criteria

### Functional Requirements
- ✅ JIRA URL → Ticket analysis complete
- ✅ Execution plan generated with checkboxes
- ✅ User approval at checkpoints
- ✅ Code implementation → Files modified
- ✅ Build & test execution automatic
- ✅ Commit & PR creation successful
- ✅ Progress saved to Serena memory
- ✅ Session resume capability

### Quality Standards
- ✅ Each mode independently executable
- ✅ Error recovery or rollback available
- ✅ Memory keys follow naming convention
- ✅ TodoWrite updates in real-time
- ✅ No critical actions without approval

### Usability
- ✅ One command = full pipeline
- ✅ Clear approval UI
- ✅ Easy progress tracking
- ✅ Clear error messages with solutions

## Important Constraints

### What This Skill Can Do
- Analyze JIRA tickets automatically
- Generate implementation plans
- Create branches and implement code
- Run builds and tests
- Create commits and PRs
- Track progress across sessions

### What This Skill Cannot Do
- Modify JIRA tickets (read-only)
- Execute without user approval at critical points
- Guarantee build/test success (depends on implementation quality)
- Work without JIRA credentials

### Assumptions
- JIRA ticket has clear requirements in description
- Development environment is set up (build tools, dependencies)
- Git repository is initialized and has remote
- GitHub CLI is authenticated

## Version History

- **1.0.0** (2026-01-07): Initial release
  - ✅ 5-mode workflow (ANALYZE, IMPLEMENT, VERIFY, SUBMIT, COMPLETE)
  - ✅ Semi-auto approval (2 checkpoints)
  - ✅ TodoWrite + Serena memory integration
  - ✅ Error recovery and rollback
  - ✅ /jira-commit and /jira-pr integration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jayleekr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
