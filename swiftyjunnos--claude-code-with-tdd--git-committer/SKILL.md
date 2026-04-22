---
name: git-committer
description: Analyze staged changes and create meaningful git commits with appropriate commit messages following conventional commit standards. Automatically groups changes into logical units and presents multiple commit options (single vs. multiple commits) before execution. Always waits for user confirmation. Use when this capability is needed.
metadata:
  author: swiftyjunnos
---

# Git Committer

Analyze staged and unstaged changes, generate meaningful commit messages following conventional commit standards, and create git commits with proper formatting.

**Key Features:**

- 🔍 Automatically analyzes changes and groups them into logical units
- 📊 Always presents multiple commit options (single vs. multiple commits)
- ⏸️ Waits for user confirmation before executing any commits
- 📝 Follows Conventional Commit standards
- 🎨 Adapts to project's existing commit style and language

## When to Use This Skill

Use this skill when:

- User asks to "commit changes", "make a commit", "git commit", "커밋해줘"
- You've completed a feature or bug fix and need to commit
- User wants to review what will be committed before committing
- You need to create a well-formatted commit message

**IMPORTANT:** This skill ALWAYS asks for user confirmation before creating commits. It will:

1. Analyze all changes
2. Group them into logical units
3. Present multiple commit strategies (single vs. multiple)
4. Wait for user to choose
5. Execute the chosen strategy

## Core Workflow

### Step 1: Analyze Current Git Status

**CRITICAL: Always check for submodules first**

**Check for submodules:**

```bash
# Check if submodules exist
git submodule status
ls -la .gitmodules 2>/dev/null

# If submodules exist, check their status
git submodule foreach 'git status'
```

**Check main repository status:**

```bash
git status
git diff --staged
git diff
```

**Gather information about:**

- **Submodules with changes** (MUST be committed first)
- Staged files (what will be committed)
- Unstaged changes (what won't be committed)
- Untracked files
- Current branch name

**If submodules have changes:**

- Identify which submodules have uncommitted changes
- Identify which submodules have new commits (not yet committed in parent)
- These MUST be handled before committing the parent repository

### Step 2: Review Recent Commits

**Check commit history for style consistency:**

```bash
git log --oneline -10
git log -1 --format='%B'
```

**Identify:**

- Commit message format (conventional commits, custom format, etc.)
- Typical message length and style
- Use of prefixes/tags (feat:, fix:, chore:, etc.)
- Language preference (English, Korean, etc.)

### Step 3: Analyze Changes and Group by Logical Units

**CRITICAL: Always analyze if changes should be split into multiple commits**

**Understand the nature of changes:**

- New feature addition
- Bug fix
- Refactoring
- Documentation update
- Test additions
- Configuration changes
- Dependency updates

**Group changes by logical units:**

Analyze all changed files and group them into logical units. Each unit should:

- Have a single, clear purpose
- Be independently understandable
- Represent one type of change (feat, fix, refactor, etc.)

**Example groupings:**

```
Group 1 (feat): New login feature
- src/pages/LoginPage.tsx
- src/hooks/useAuth.ts
- src/routes.tsx

Group 2 (test): Login feature tests
- tests/LoginPage.test.tsx
- tests/useAuth.test.ts

Group 3 (docs): Update authentication documentation
- docs/authentication.md
- README.md
```

**Important considerations:**

- Check for sensitive files (.env, credentials, secrets)
- Ensure tests pass (if applicable)
- Check for console.log or debug code
- **Identify if changes can be logically separated into multiple commits**
- **Look for different types (feat + fix), different scopes (auth + ui), or different concerns**

### Step 4: Determine Commit Strategy

**IMPORTANT: Always present commit options to the user before executing**

**CRITICAL: Check for submodule changes first**

If submodules have changes:

- **Separate submodule commits from parent repository commits**
- Submodules are ALWAYS committed first
- Parent repository commit includes submodule reference update

**Single commit criteria:**

- All changes are tightly coupled
- Changes represent one atomic unit
- Separating would break functionality

**Multiple commits criteria:**

- Changes span different features/modules
- Mix of different types (feat + fix + refactor)
- Tests can be separated from implementation
- Documentation updates are substantial
- Mix of related but independent changes
- **Submodule changes vs. parent repository changes**

**Always ask the user:**

If changes can be split, present options like:

```markdown
## 📋 변경사항 분석

**변경된 파일들을 다음과 같이 그룹화할 수 있습니다:**

### 옵션 A: 단일 커밋 (빠른 방법)
```

feat(auth): implement login feature with tests and docs

- Add LoginPage component
- Create useAuth hook
- Update routes configuration
- Add tests for login functionality
- Update authentication documentation

```

### 옵션 B: 3개의 커밋으로 분리 (권장)

**커밋 1:**
```

feat(auth): implement login page and authentication hook

```
파일: LoginPage.tsx, useAuth.ts, routes.tsx

**커밋 2:**
```

test(auth): add tests for login functionality

```
파일: LoginPage.test.tsx, useAuth.test.ts

**커밋 3:**
```

docs(auth): update authentication documentation

```
파일: authentication.md, README.md

### 옵션 C: 커스텀
직접 커밋을 어떻게 나눌지 알려주세요.

---
어떤 방식으로 진행할까요? (A/B/C)
```

**Wait for user response before proceeding**

### Step 5: Generate Commit Messages

**Follow conventional commit format:**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, missing semicolons, etc.)
- `refactor`: Code refactoring
- `test`: Adding or modifying tests
- `chore`: Maintenance tasks, dependency updates
- `perf`: Performance improvements
- `ci`: CI/CD changes
- `build`: Build system changes

**Guidelines:**

- Subject line: 50 characters or less, imperative mood
- Body: Explain the "why" not the "what" (optional)
- Footer: Reference issues, breaking changes (optional)
- Use consistent language with project history
- Be specific and descriptive

### Step 6: Handle Submodules First (If Applicable)

**CRITICAL: If submodules have changes, handle them BEFORE committing parent repository**

#### Submodule Workflow:

**1. Identify submodules with changes:**

```bash
# Check each submodule
git submodule foreach 'git status --short'
```

**2. For each submodule with changes, present commit options:**

```markdown
## 🔗 서브모듈 커밋 필요

**.claude/skills 서브모듈**에 변경사항이 있습니다.
먼저 서브모듈을 커밋한 후, 루트 레포지토리를 커밋해야 합니다.

### 서브모듈 변경사항:

- .claude/skills/git-committer/SKILL.md (new)

**서브모듈 커밋 메시지:**
```

feat: add git-committer skill

Add new git-committer skill that analyzes changes and creates
meaningful commits with conventional commit standards. Supports
multiple commit strategies and submodule handling.

```

---
이 내용으로 서브모듈을 먼저 커밋할까요? (Y/n)
```

**3. Execute submodule commits:**

```bash
# Navigate to submodule and commit
cd .claude/skills
git add git-committer/SKILL.md
git commit -m "feat: add git-committer skill" -m "Add new git-committer skill that analyzes changes and creates meaningful commits with conventional commit standards. Supports multiple commit strategies and submodule handling."

# Return to root
cd ../..

# Update parent repository to reference new submodule commit
git add .claude/skills
```

**4. Notify user:**

```markdown
✅ 서브모듈 커밋 완료

**.claude/skills** 서브모듈이 성공적으로 커밋되었습니다.

이제 루트 레포지토리를 커밋할 준비가 되었습니다.
루트 레포지토리에는 서브모듈 참조 업데이트가 포함됩니다.
```

**5. Continue to Step 7 for parent repository commit**

### Step 7: Execute Parent Repository Commits Based on User Choice

**IMPORTANT: Only execute after user confirms the commit strategy**

**Note:** If submodules were committed in Step 6, ensure the parent commit includes the submodule reference update.

#### For Single Commit (Option A):

```bash
# Stage all relevant files
git add <files>

# Create the commit
git commit -m "feat(auth): implement login feature with tests and docs" -m "- Add LoginPage component
- Create useAuth hook
- Update routes configuration
- Add tests for login functionality
- Update authentication documentation"

# Verify
git log -1 --stat
```

#### For Multiple Commits (Option B):

**Execute commits sequentially:**

```bash
# Commit 1: Implementation
git add src/pages/LoginPage.tsx src/hooks/useAuth.ts src/routes.tsx
git commit -m "feat(auth): implement login page and authentication hook" -m "Add LoginPage component with email/password form and useAuth hook for authentication state management. Update routes to include new login page."

# Commit 2: Tests
git add tests/LoginPage.test.tsx tests/useAuth.test.ts
git commit -m "test(auth): add tests for login functionality" -m "Add comprehensive tests for LoginPage component and useAuth hook covering success and error scenarios."

# Commit 3: Documentation
git add docs/authentication.md README.md
git commit -m "docs(auth): update authentication documentation" -m "Document new login feature, authentication flow, and usage examples."

# Verify all commits
git log -3 --oneline
```

#### For Custom Split (Option C):

Follow user's instructions for grouping files and creating commits.

### Step 7: Verify and Report

After completing commit(s), show summary:

```markdown
## ✅ 커밋 완료

**생성된 커밋:**

- abc1234 feat(auth): implement login page and authentication hook
- def5678 test(auth): add tests for login functionality
- ghi9012 docs(auth): update authentication documentation

**다음 단계:**

- [ ] 코드 리뷰 필요한가요?
- [ ] 원격 저장소에 push할까요?
- [ ] PR을 생성할까요?
```

**Important notes:**

- NEVER commit sensitive files (.env, credentials, secrets)
- NEVER skip pre-commit hooks unless explicitly requested
- NEVER amend commits from other developers
- ALWAYS verify the commit was created successfully
- ALWAYS wait for user confirmation before executing commits

## Special Cases

### Multiple Logical Changes

**This is now handled automatically in Step 3 and Step 4.**

The skill will always analyze and present multiple commit options when applicable. This section is kept for reference on how to handle edge cases.

### Pre-commit Hook Failures

If pre-commit hooks modify files:

```bash
# Check if it's safe to amend
git log -1 --format='%an %ae'
git status

# If the last commit is yours and not pushed
git add .
git commit --amend --no-edit -m "$(git log -1 --format='%B')"

# Otherwise create a new commit
git add .
git commit -m "chore: apply pre-commit hook changes"
```

### Empty Changes

If no changes to commit:

```markdown
현재 커밋할 변경사항이 없습니다.

**Git Status:**

- Staged files: 0
- Unstaged changes: 0
- Untracked files: [list if any]

다음 중 선택해주세요:
A) Untracked 파일을 stage하고 커밋
B) 작업 디렉토리 상태 확인
C) 취소
```

## Examples

### Example 1: Feature Addition

**User:** "커밋해줘"

**Analysis:**

```bash
$ git status
On branch feature/login
Changes to be committed:
  new file:   src/pages/LoginPage.tsx
  new file:   src/hooks/useAuth.ts
  modified:   src/routes.tsx

$ git log --oneline -5
abc1234 feat: 유저 프로필 페이지 추가
def5678 fix: 네비게이션 버그 수정
ghi9012 refactor: API 클라이언트 복잡도 해소
```

**Output:**

```markdown
## 📋 변경사항 분석

**변경된 파일:**

- src/pages/LoginPage.tsx (new)
- src/hooks/useAuth.ts (new)
- src/routes.tsx (modified)

**분석 결과:**

- ✅ 모든 변경사항이 인증 기능과 관련됨
- ✅ 민감한 파일 없음
- ✅ 프로젝트 커밋 스타일 준수 (한글)

### 옵션 A: 단일 커밋 (권장)
```

feat(auth): 로그인 페이지 및 인증 훅 구현

이메일/비밀번호 폼이 있는 LoginPage 컴포넌트와 인증 상태
관리를 위한 useAuth 훅을 추가. 새 로그인 페이지를 포함하도록
라우트 업데이트.

```

---
이 내용으로 커밋할까요? (Y/n)
```

### Example 2: Bug Fix

**Git diff shows:**

```diff
- if (user.age > 18) {
+ if (user.age >= 18) {
```

**Proposed message:**

```
fix(validation): correct age verification logic

Change age check from > to >= to properly include 18-year-olds
in adult verification. Fixes issue where 18-year-olds were
incorrectly rejected.

Fixes #123
```

### Example 3: Multiple Logical Units (Multiple Commits Recommended)

**User:** "커밋해줘"

**Analysis:**

```bash
$ git status
Changes to be committed:
  modified:   src/api/rateLimit.ts
  new file:   tests/rateLimit.test.ts
  modified:   docs/api.md
  modified:   README.md
  new file:   config/rateLimit.config.ts
```

**Output:**

```markdown
## 📋 변경사항 분석

**변경된 파일들을 다음과 같이 그룹화할 수 있습니다:**

### 그룹 1 (feat): Rate limiting 구현

- src/api/rateLimit.ts
- config/rateLimit.config.ts

### 그룹 2 (test): Rate limiting 테스트

- tests/rateLimit.test.ts

### 그룹 3 (docs): API 문서 업데이트

- docs/api.md
- README.md

---

### 옵션 A: 단일 커밋 (빠른 방법)
```

feat(api): rate limiting 기능 추가 (테스트 및 문서 포함)

express-rate-limit를 사용하여 rate limiting 미들웨어 구현.
엔드포인트별 다른 제한을 지원하는 설정 추가.
단위 테스트, 통합 테스트 및 API 문서 업데이트 포함.

```

### 옵션 B: 3개의 커밋으로 분리 (권장) ⭐

**커밋 1:**
```

feat(api): rate limiting 미들웨어 구현

express-rate-limit를 사용하여 rate limiting 미들웨어 추가.
엔드포인트별로 다른 제한을 설정할 수 있는 설정 파일 포함.

```
파일: src/api/rateLimit.ts, config/rateLimit.config.ts

**커밋 2:**
```

test(api): rate limiting 테스트 추가

rate limiting 미들웨어에 대한 단위 테스트 및 통합 테스트 추가.
다양한 시나리오 (제한 초과, 정상 요청 등) 커버.

```
파일: tests/rateLimit.test.ts

**커밋 3:**
```

docs(api): rate limiting 설정 문서화

새로운 rate limiting 기능에 대한 문서 추가.
설정 방법, 사용 예제, 제한 정책 설명 포함.

```
파일: docs/api.md, README.md

### 옵션 C: 커스텀
직접 커밋을 어떻게 나눌지 알려주세요.

---
어떤 방식으로 진행할까요? (A/B/C)
```

### Example 4: With Submodules (Submodule First, Then Parent)

**User:** "커밋해줘"

**Analysis:**

```bash
$ git submodule status
+abc1234 .claude/skills (heads/main)

$ git status
Changes to be committed:
  renamed:    .claude/claude-skills -> .claude/skills
  modified:   .gitmodules

Changes not staged for commit:
  modified:   .claude/skills (new commits, untracked content)

$ cd .claude/skills && git status
On branch main
Untracked files:
  git-committer/SKILL.md
```

**Output:**

```markdown
## 📋 변경사항 분석

### 🔗 서브모듈 변경사항 감지

**.claude/skills 서브모듈**에 커밋되지 않은 변경사항이 있습니다.
**먼저 서브모듈을 커밋한 후**, 루트 레포지토리를 커밋해야 합니다.

---

## 1단계: 서브모듈 커밋 (.claude/skills)

**서브모듈 변경사항:**

- git-committer/SKILL.md (new)

**제안 커밋 메시지:**
```

feat: add git-committer skill with submodule support

Add git-committer skill that analyzes changes and creates
meaningful commits following conventional commit standards.
Includes automatic grouping into logical units, multiple
commit strategies, and submodule-first commit workflow.

```

---
이 내용으로 서브모듈을 먼저 커밋할까요? (Y/n)

---

## 2단계: 루트 레포지토리 커밋

**서브모듈 커밋 후 진행될 내용:**

**루트 레포지토리 변경사항:**
- .claude/claude-skills → .claude/skills (renamed)
- .gitmodules (modified)
- .claude/skills (submodule reference update)

**제안 커밋 메시지:**
```

chore: migrate claude-skills to skills directory

Rename .claude/claude-skills to .claude/skills for better
naming consistency. Update .gitmodules and submodule reference.

```

---
(서브모듈 커밋 완료 후 자동으로 진행됩니다)
```

**Execution Flow:**

```bash
# Step 1: Commit submodule
cd .claude/skills
git add git-committer/SKILL.md
git commit -m "feat: add git-committer skill with submodule support"
cd ../..

# Step 2: Update parent to reference new submodule commit
git add .claude/skills

# Step 3: Commit parent repository
git add .gitmodules
git commit -m "chore: migrate claude-skills to skills directory"

# Verify
git log -2 --oneline
git submodule status
```

## Best Practices

### DO ✅

- **Always check for submodules first** and commit them before parent
- Always review changes before committing
- Follow the project's existing commit message style
- Keep commits atomic (one logical change per commit)
- Write clear, descriptive messages
- Include issue/ticket references when applicable
- Verify tests pass before committing
- Check for and warn about sensitive files
- **Separate submodule commits from parent repository commits**
- Update parent repository to reference new submodule commits

### DON'T ❌

- **Don't commit parent repository before committing submodules**
- Don't commit unrelated changes together
- Don't use vague messages like "update" or "fix stuff"
- Don't commit sensitive information
- Don't skip hooks without user permission
- Don't amend other developers' commits
- Don't commit non-working code
- Don't batch multiple logical changes unnecessarily
- **Don't forget to update submodule references in parent repository**

## Configuration

### Custom Commit Templates

If project has a custom commit template:

```bash
git config --get commit.template
```

Use the template format if it exists.

### Commit Message Language

Follow the project's language preference:

- Check recent commits for language consistency
- Ask user if unclear: "커밋 메시지를 한글로 작성할까요, 영어로 작성할까요?"

### Sign-off Requirements

Some projects require sign-off:

```bash
git commit -s -m "message"
```

Check project's CONTRIBUTING.md for requirements.

## Integration with Development Workflow

### Before Committing

1. ✅ **Check for submodules** (git submodule status)
2. ✅ Run tests (if applicable)
3. ✅ Run linter/formatter
4. ✅ Review all changes
5. ✅ Remove debug code
6. ✅ Update documentation if needed

### After Committing

1. Show commit details: `git show --stat`
2. Confirm next steps:
   - Push to remote?
   - Create pull request?
   - Continue development?

## Error Handling

### Common Issues and Solutions

**Merge conflicts:**

```markdown
⚠️ 현재 merge conflict가 있습니다.
먼저 conflict를 해결한 후에 커밋할 수 있습니다.

Conflicted files:

- src/App.tsx

다음 명령어로 확인하세요:
$ git status
$ git diff
```

**Detached HEAD:**

```markdown
⚠️ Detached HEAD 상태입니다.
커밋하기 전에 브랜치를 생성하거나 체크아웃하세요.

$ git checkout -b new-branch-name
또는
$ git checkout existing-branch
```

**Nothing to commit:**

```markdown
ℹ️ 커밋할 변경사항이 없습니다.
모든 변경사항이 이미 커밋되었거나 stage되지 않았습니다.
```

**Submodule not initialized:**

```markdown
⚠️ 서브모듈이 초기화되지 않았습니다.

다음 명령어로 서브모듈을 초기화하세요:
$ git submodule init
$ git submodule update
```

**Submodule has unpushed commits:**

```markdown
⚠️ 서브모듈에 푸시되지 않은 커밋이 있습니다.

서브모듈 커밋을 원격에 푸시한 후 부모 레포지토리를 커밋하는 것을 권장합니다.
그렇지 않으면 다른 개발자들이 서브모듈 커밋을 가져올 수 없습니다.

옵션:
A) 서브모듈을 원격에 푸시하고 부모 레포지토리 커밋
B) 로컬에만 커밋 (나중에 푸시)

어떻게 하시겠습니까? (A/B)
```

**Submodule detached HEAD:**

```markdown
⚠️ 서브모듈이 Detached HEAD 상태입니다.

서브모듈의 변경사항을 브랜치에 커밋해야 합니다.

$ cd .claude/skills
$ git checkout -b feature/new-skill
또는
$ git checkout main
```

## Summary

This skill helps create meaningful, well-formatted git commits by:

1. **Checking for submodules first** and handling them before parent repository
2. Analyzing current changes thoroughly and grouping into logical units
3. **Presenting multiple commit strategies** (single vs. multiple commits)
4. Following project conventions and commit message standards
5. Generating descriptive commit messages with Conventional Commit format
6. Ensuring code quality and safety (no sensitive files, proper validation)
7. **Waiting for user confirmation** before executing any commits
8. Providing clear feedback and next steps to users

**Commit Order:**

1. Submodules (if any changes)
2. Parent repository (including submodule reference updates)

Always prioritize code quality, security, clear communication, and proper submodule handling throughout the commit process.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/swiftyjunnos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
