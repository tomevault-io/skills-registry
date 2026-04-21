---
name: github-deployer
description: This skill handles GitHub deployment for BITDA ERP UI code. Use this skill after ui-designer completes code generation to push code to pre-publishing repository. Triggers on requests like "GitHub에 배포해줘", "코드 푸시해줘", "배포해줘", "PR 만들어줘", "깃헙에 올려줘". This skill does NOT register to Notion DB - use notion-uploader skill for that after confirming the deployment. Use when this capability is needed.
metadata:
  author: invigoworks
---

# GitHub Deployer

## Overview

This skill handles the GitHub deployment phase for BITDA ERP UI code:

1. **Code Analysis**: Analyze and organize generated code from ui-designer
2. **GitHub Push**: Push code to feature branches in pre-publishing repository
3. **PR Creation**: Create pull requests for review (optional)

Target repository: `invigoworks/pre-publishing`

## Prerequisites

- **GitHub CLI (gh)**: Authenticated with `invigoworks-dev` account
  ```bash
  # 인증 상태 확인
  gh auth status

  # invigoworks-dev 계정으로 로그인
  gh auth login
  ```
- **Repository Access**: `invigoworks/pre-publishing` 저장소 쓰기 권한
- Generated code from ui-designer skill (또는 직접 작성한 코드)

## Reference Files

- `references/deployment-config.md`: Deployment configuration and branch naming conventions
- `references/readme-template.md`: README.md 템플릿 및 업데이트 가이드

---

## Specialized Agent Integration

This skill leverages invigo-agents for deployment quality and automation:

### Recommended Agents by Task

| Task | Agent | Purpose |
|------|-------|---------|
| DevOps | `invigo-agents:devops-engineer` | CI/CD, deployment automation, infrastructure |
| Code Review | `invigo-agents:code-reviewer` | Pre-deployment quality check |
| Deployment | `invigo-agents:deployment-engineer` | Pipeline configuration, container management |
| Debugging | `invigo-agents:debugger` | Deployment issue investigation |

### Agent Invocation Strategy

**Before Deployment:**

1. **Pre-Deployment Review** - Check code quality:
   ```
   Task(subagent_type="invigo-agents:code-reviewer")
   Prompt: "Review code changes before deployment to pre-publishing.
   Check for security vulnerabilities, performance issues, and best practices."
   ```

2. **Deployment Automation** - For complex deployments:
   ```
   Task(subagent_type="invigo-agents:devops-engineer")
   Prompt: "Configure deployment pipeline for [기능명].
   Include pre-commit hooks, build validation, and deployment scripts."
   ```

**During Deployment Issues:**

3. **Error Investigation** - When push fails:
   ```
   Task(subagent_type="invigo-agents:debugger")
   Prompt: "Investigate deployment failure for [브랜치명].
   Analyze error logs and suggest fixes."
   ```

### Pre-Deployment Checklist with Agents

Before pushing code:

```typescript
// Pre-deployment quality gate
const preDeployCheck = Task({
  subagent_type: "invigo-agents:code-reviewer",
  prompt: `Review the following files for deployment readiness:
  - Check import statements and dependencies
  - Verify no console.logs or debug code
  - Ensure proper error handling
  - Validate TypeScript types are correct`
});
```

### CI/CD Enhancement

For automated deployment pipelines:

```typescript
// DevOps pipeline configuration
const pipelineSetup = Task({
  subagent_type: "invigo-agents:deployment-engineer",
  prompt: `Configure GitHub Actions workflow for [기능명]:
  - Build validation on PR
  - Type checking with tsc
  - Lint with ESLint
  - Unit test execution`
});
```

---

## Workflow

### Phase 1: Code Preparation

Before deployment, verify the generated files:

1. **List Generated Files**: Identify all files from ui-designer
2. **Verify File Structure**: Confirm proper directory structure
3. **Check Dependencies**: Identify any new dependencies to add

Output format:

```markdown
## 배포 파일 목록

| 파일 경로 | 유형 | 설명 |
|----------|------|------|
| src/app/work-orders/page.tsx | Page | 목록 화면 |
| src/app/work-orders/components/WorkOrderSheet.tsx | Component | 등록/수정 폼 |
| src/app/work-orders/components/columns.tsx | Config | 테이블 컬럼 정의 |
| src/lib/validations/work-order.ts | Validation | Zod 스키마 |
```

### Phase 2: Repository Setup

#### 2.1 Clone/Update Repository

```bash
# Clone if not exists
gh repo clone invigoworks/pre-publishing /tmp/pre-publishing -- --depth 1

# Or update if exists
cd /tmp/pre-publishing
git fetch origin
git checkout main
git pull origin main
```

#### 2.2 Create Feature Branch

Branch naming convention: `feature/[기능코드]-[기능명-영문]`

```bash
git checkout -b feature/[기능코드]-[기능명]
```

Examples:
- `feature/PRD-WO-work-orders` (작업지시)
- `feature/MST-ITEM-products` (제품관리)
- `feature/ADM-USR-users` (사용자관리)

### Phase 3: File Deployment

#### 3.1 Create Directory Structure

```bash
mkdir -p src/app/[feature-name]/components
mkdir -p src/lib/validations
mkdir -p src/components/shared
```

#### 3.2 Write Files

Use the Write tool to create each file in the repository.

#### 3.3 Handle Shared Components

If reusable components are identified:

1. Check if component already exists in `src/components/shared/`
2. If new, create the shared component
3. Update imports in feature components

#### 3.4 Update README.md (필수)

**모든 배포 시 README.md를 업데이트하여 구현된 기능 목록 유지:**

1. 저장소 루트의 `README.md` 파일 확인
2. "구현된 기능" 또는 "Implemented Features" 섹션에 새 기능 추가
3. 화면 코드, 기능명, 상태 포함

**README.md 업데이트 형식:**

```markdown
## 구현된 기능 (Implemented Features)

| 기능 코드 | 기능명 | 화면 | 상태 | 날짜 |
|-----------|--------|------|------|------|
| PRD-WO | 작업지시 | S001, F001, P001 | ✅ 완료 | 2025-01-12 |
| MST-ITEM | 제품관리 | S001, F001 | ✅ 완료 | 2025-01-10 |
| [기능코드] | [기능명] | [화면목록] | ✅ 완료 | [날짜] |
```

**README.md가 없는 경우 새로 생성:**

```markdown
# Pre-Publishing Repository

BITDA ERP UI 코드 사전 검토용 저장소

## 구현된 기능 (Implemented Features)

| 기능 코드 | 기능명 | 화면 | 상태 | 날짜 |
|-----------|--------|------|------|------|
| [기능코드] | [기능명] | [화면목록] | ✅ 완료 | [날짜] |

## 기술 스택

- Next.js (App Router)
- TypeScript
- shadcn/ui
- React Hook Form + Zod
- TanStack Table

## 검토 프로세스

1. 기능 브랜치에서 코드 확인
2. 디자인/기능 검토
3. 수정 사항 반영
4. PR 승인 후 main 병합

---

🤖 Generated with Claude Code
```

**README 업데이트 자동화 (권장):**

```bash
# 현재 날짜 가져오기
TODAY=$(date +%Y-%m-%d)

# README.md에 새 기능 행 추가 (테이블 마지막에)
# sed 또는 직접 파일 편집으로 처리
```

### Phase 4: Git Operations

#### 4.1 Stage and Commit

```bash
git add .
git commit -m "feat([기능코드]): [기능명] 화면 구현

- 목록 화면 (S001)
- 등록/수정 Sheet (F001)
- 확인 팝업 (P001)

화면 코드:
- BITDA-CM-PRD-WO-S001
- BITDA-CM-PRD-WO-F001
- BITDA-CM-PRD-WO-P001

🤖 Generated with Claude Code

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

#### 4.2 Push to Remote

```bash
git push -u origin feature/[기능코드]-[기능명]
```

### Phase 5: Pull Request Creation (Optional)

#### 5.1 Create PR

```bash
gh pr create \
  --title "feat([기능코드]): [기능명] 화면 구현" \
  --body "$(cat <<'EOF'
## Summary
[기능명] 기능의 UI 화면을 구현했습니다.

## 구현 화면
| 화면명 | 화면 코드 | 유형 |
|--------|----------|------|
| [화면명1] | BITDA-XX-XX-XX-S001 | 목록 |
| [화면명2] | BITDA-XX-XX-XX-F001 | 등록/수정 |
| [화면명3] | BITDA-XX-XX-XX-P001 | 팝업 |

## 기술 스택
- Next.js (App Router)
- shadcn/ui
- React Hook Form + Zod

## 테스트 체크리스트
- [ ] 목록 화면 렌더링
- [ ] 등록 폼 동작
- [ ] 수정 폼 동작
- [ ] 삭제 확인 팝업
- [ ] 반응형 레이아웃

🤖 Generated with Claude Code
EOF
)"
```

---

## Commit Convention

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | 설명 | Example |
|------|-----|---------|
| feat | 새 기능 | `feat(PRD-WO): 작업지시 목록 화면 추가` |
| fix | 버그 수정 | `fix(MST-ITEM): 제품 저장 오류 수정` |
| refactor | 리팩토링 | `refactor(shared): SearchableSelect 최적화` |
| style | 스타일 변경 | `style(ui): 버튼 호버 효과 개선` |
| docs | 문서 변경 | `docs: README 업데이트` |

### Footer

Always include:

```
🤖 Generated with Claude Code

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

---

## Post-Deployment Output

After successful deployment, provide:

```markdown
## 배포 완료

### Git 작업
- 브랜치: feature/[기능코드]-[기능명] ✓
- 파일 생성: [N]개 파일 ✓
- 커밋: feat([기능코드]): [기능명] 화면 구현 ✓
- 푸시: origin/feature/[기능코드]-[기능명] ✓

### PR (선택사항)
- PR URL: https://github.com/invigoworks/pre-publishing/pull/[번호]

### 다음 단계
1. 브라우저에서 코드 확인
2. 디자인/기능 검토 및 수정
3. 검토 완료 후 `/notion-uploader` 스킬로 Notion DB 등록
```

---

## Error Handling

### GitHub 인증 문제 해결

**invigoworks-dev 계정으로 gh 로그인:**

```bash
# 현재 로그인 상태 확인
gh auth status

# 로그아웃 (다른 계정 로그인 시)
gh auth logout

# invigoworks-dev 계정으로 로그인
gh auth login
# 선택 옵션:
# - GitHub.com
# - HTTPS
# - Login with a web browser (또는 Paste an authentication token)
# - 브라우저에서 invigoworks-dev 계정으로 로그인
```

**Personal Access Token (PAT) 사용:**

```bash
# PAT로 직접 인증
gh auth login --with-token < ~/.github/invigoworks-dev-token.txt

# 또는 환경변수 설정
export GH_TOKEN="ghp_xxxxxxxxxxxxxxxxxxxx"
```

### 일반적인 오류 및 해결 방법

| 오류 | 원인 | 해결 방법 |
|------|------|----------|
| `gh: command not found` | gh CLI 미설치 | `brew install gh` |
| `HTTP 401` | 인증 실패 | `gh auth login` 재실행 |
| `HTTP 403` | 권한 부족 | invigoworks-dev 계정으로 재로그인 |
| `Repository not found` | 저장소 접근 권한 없음 | 계정 확인 및 권한 요청 |
| `Branch already exists` | 브랜치 중복 | 기존 브랜치 사용 또는 새 이름 지정 |
| `Push rejected` | 충돌 발생 | `git pull --rebase` 후 재시도 |
| `Connection refused` | 네트워크 문제 | VPN 확인, 네트워크 재연결 |

### SSH 연결 문제

```bash
# SSH 연결 테스트
ssh -T git@github.com

# SSH 키 확인
ls -la ~/.ssh/

# SSH 키 생성 (필요 시)
ssh-keygen -t ed25519 -C "invigoworks.dev@gmail.com"

# SSH 에이전트에 키 추가
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 권한 문제 지속 시

1. GitHub 웹에서 invigoworks-dev 계정으로 로그인
2. `invigoworks/pre-publishing` 저장소 접근 확인
3. Settings > Developer settings > Personal access tokens에서 토큰 재발급
4. 토큰 권한: `repo`, `workflow` 필수 체크

---

## Usage Examples

**Example 1: Push and Create PR**
```
User: 작업지시 화면 코드 GitHub에 배포하고 PR 만들어줘

Response:
## 배포 완료

### Git 작업
- 브랜치: feature/PRD-WO-work-orders ✓
- 파일 생성: 4개 파일 ✓
- 커밋: feat(PRD-WO): 작업지시 화면 구현 ✓
- 푸시: origin/feature/PRD-WO-work-orders ✓

### PR
- PR URL: https://github.com/invigoworks/pre-publishing/pull/123

검토 후 수정이 필요하면 말씀해주세요.
Notion DB 등록은 `/notion-uploader` 스킬을 사용해주세요.
```

**Example 2: Push Only (No PR)**
```
User: 코드만 푸시해줘, PR은 나중에

Response:
## 배포 완료

### Git 작업
- 브랜치: feature/PRD-WO-work-orders ✓
- 푸시 완료 ✓

브랜치 URL: https://github.com/invigoworks/pre-publishing/tree/feature/PRD-WO-work-orders

PR 생성이나 Notion 등록이 필요하면 말씀해주세요.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invigoworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
