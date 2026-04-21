---
name: dev-feature-planner
description: SKILL.md의 모든 표준(문서화, 리스크 평가, 롤백 전략, 엄격한 TDD)을 준수하는 고급 기능 구현 워크플로우입니다. Use when this capability is needed.
metadata:
  author: lastdays03
---

# Expert Feature Planner Workflow

이 워크플로우는 `this document`의 "Feature Planner" 표준을 완벽하게 구현하도록 설계되었습니다. 단순한 구현이 아닌, 엔터프라이즈급 안정성과 문서화를 지향합니다.

### 1단계: 심층 분석 및 문맥 파악 (Deep Assessment)
1.  **Context Loading**: `this document`와 `resources/plan-template.md`를 읽어 '품질 기준'을 재확인합니다.
2.  **Codebase Analysis**:
    *   구현할 기능과 관련된 기존 코드, 의존성, 아키텍처를 분석합니다.
    *   `list_dir`와 `view_file_outline`을 사용하여 영향 범위를 파악합니다.

### 2단계: 마스터 플랜 문서 생성 (Master Plan Creation)
*단순한 `implementation_plan.md`가 아닌, 영구 보존 가능한 상세 기획 문서를 작성합니다.*

1.  **Setup**: `docs/plans/` 디렉토리가 없다면 생성합니다.
2.  **Drafting**: `resources/plan-template.md`의 **모든 섹션**을 활용하여 `docs/plans/PLAN_[기능명].md` 파일을 작성합니다.
    *   **Architecture Decisions**: 트레이드오프 분석을 반드시 포함합니다.
    *   **Test Strategy**: 유닛/통합/E2E 테스트 범위를 `Pyramid` 구조로 정의합니다.
    *   **Phase Breakdown**: 3~7개의 단계로 나누되, 각 단계는 `Red -> Green -> Refactor` 사이클을 명시해야 합니다.
    *   **Risk Assessment**: 예상되는 기술적/일정 리스크와 완화 전략을 작성합니다.
    *   **Exception Handling Strategy**: 사용자 정의 예외, 로깅 레벨, 사용자 피드백 전략을 정의합니다.
    *   **Rollback Strategy**: 각 단계별 실패 시 복구 절차를 정의합니다.
3.  **Cross-Check**: 생성된 계획이 `task.md`와 동기화되도록 `task.md`도 업데이트합니다.

### 3단계: 승인 및 품질 게이트 설정 (Approval & Gates)
1.  `notify_user`를 사용하여 작성된 `docs/plans/PLAN_[기능명].md`의 검토를 요청합니다.
    *   메시지에 "리스크 평가 및 롤백 전략을 포함한 상세 계획이 준비되었습니다"라고 명시합니다.
    *   `BlockedOnUser: true`로 설정하여 명시적 승인을 받습니다.

### 4단계: 엄격한 TDD 실행 루프 (Rigorous TDD Execution)
계획의 각 Phase에 대해 다음 순서를 **절대적으로** 준수합니다:

0.  **🧹 Pre-flight Cleanup**
    *   새로운 작업을 시작하기 전, 이전 프로세스의 잔재(포트 점유 등)가 없는지 확인하고 정리합니다.
    *   `lsof -i :<port>` 등을 활용합니다.

1.  **🔴 RED (Test First)**
    *   `implementation_plan.md`나 별도 문서가 아닌, **실제 테스트 코드**를 먼저 작성합니다.
    *   테스트를 실행하여 **실패(Fail)**함을 확인하고, 그 결과를 캡처합니다 (터미널 출력 확인).
    *   *Quality check*: 테스트가 비즈니스 로직을 정확히 검증하고 있습니까?
    *   **Exception Check**: 예외 발생 시나리오(Edge Case)에 대한 테스트가 포함되어 있습니까?
    *   **Rescue Path**: 테스트 실패 원인을 모르겠거나 구현이 막히면, `/dev-coding-debug`를 호출하여 원인을 분석합니다.

2.  **🟢 GREEN (Implementation)**
    *   테스트를 통과시키기 위한 **최소한의 코드**만 작성합니다.
    *   테스트를 재실행하여 **통과(Pass)**함을 확인합니다.

3.  **🔵 REFACTOR (Clean Up)**
    *   기능 변경 없이 코드 구조, 네이밍, 중복을 개선합니다.
    *   **Cleanup Checklist**를 반드시 확인하고 체크합니다:
        - [ ] 중복 제거 (DRY)
        - [ ] 네이밍 개선
        - [ ] 재사용 컴포넌트 추출
        - [ ] 문서화 추가
    *   개선 후 테스트가 여전히 통과하는지 확인합니다.
    *   `task_boundary`를 업데이트하여 진척도를 기록합니다.

4.  **🚧 Phase Quality Gate**
    *   해당 Phase 완료 전, `SKILL.md`의 "Quality Gate Standards"를 검증합니다.
    *   (빌드 성공, 린트 통과, 테스트 커버리지 충족, 보안 취약점 없음)

### 5단계: 최종 인도 및 문서화 (Final Delivery)
1.  **Full Regression Test**: 전체 테스트 스위트를 실행하여 사이드 이펙트가 없는지 확인합니다.
2.  **Browser Verification** (UI 기능인 경우 필수):
    *   `browser_subagent`를 실행하여 구현된 페이지나 컴포넌트를 직접 방문합니다.
    *   렌더링 상태, 레이아웃, 기본 인터랙션을 확인하고 스크린샷을 캡처합니다.
3.  **Walkthrough Artifact**:
    *   `walkthrough.md`에 최종 결과물, 테스트 결과 요약(커버리지 리포트 등), **브라우저 스크린샷 및 녹화**를 포함합니다.
3.  **Plan Completion**: `docs/plans/PLAN_[기능명].md`의 상태를 'Complete'로 업데이트하고 'Lessons Learned' 섹션을 채웁니다.
4.  **Documentation Update**: 작업 내용을 반영하여 문서를 최신화합니다.
    *   `/dev-doc-suite`를 호출하여 README.md 및 관련 API 문서를 업데이트합니다.
5.  **Cleanup**: 브라우저 탭/창을 닫고, 테스트를 위해 실행한 백그라운드 서버 프로세스를 종료합니다.
6.  **사용자 알림**: 사용자에게 완료를 알립니다.

---

## Standards & Rules

# Feature Planner

## Purpose
Generate structured, phase-based plans where:
- Each phase delivers complete, runnable functionality
- Quality gates enforce validation before proceeding
- User approves plan before any work begins
- Progress tracked via markdown checkboxes
- Each phase is 1-4 hours maximum

## Planning Workflow

### Step 1: Requirements Analysis
1. Read relevant files to understand codebase architecture
2. Identify dependencies and integration points
3. Assess complexity and risks
4. Determine appropriate scope (small/medium/large)

### Step 2: Phase Breakdown with TDD Integration
Break feature into 3-7 phases where each phase:
- **Test-First**: Write tests BEFORE implementation
- Delivers working, testable functionality
- Takes 1-4 hours maximum
- Follows Red-Green-Refactor cycle
- Has measurable test coverage requirements
- Can be rolled back independently
- Has measurable test coverage requirements
- Can be rolled back independently
- Has clear success criteria
- **Exception Scenarios Explained**: Error cases explicitly defined and handled

**Phase Structure**:
- Phase Name: Clear deliverable
- Goal: What working functionality this produces
- **Test Strategy**: What test types, coverage target, test scenarios
- Tasks (ordered by TDD workflow):
  1. **RED Tasks**: Write failing tests first
  2. **GREEN Tasks**: Implement minimal code to make tests pass
  3. **REFACTOR Tasks**: Improve code quality while tests stay green
     - [ ] Remove duplication (DRY principle)
     - [ ] Improve naming clarity
     - [ ] Extract reusable components
     - [ ] Add inline documentation
     - [ ] Optimize performance if needed
- **Exception Tasks**:
  - [ ] Define custom exceptions
  - [ ] Implement error logging
  - [ ] Verify error messages are user-friendly
- Quality Gate: TDD compliance + validation criteria
- Dependencies: What must exist before starting
- **Coverage Target**: Specific percentage or checklist for this phase

### Step 3: Plan Document Creation
Use plan-template.md to generate: `docs/plans/PLAN_<feature-name>.md`

Include:
- Overview and objectives
- Architecture decisions with rationale
- Complete phase breakdown with checkboxes
- Quality gate checklists
- Risk assessment table
- Rollback strategy per phase
- Progress tracking section
- Notes & learnings area

### Step 4: User Approval
**CRITICAL**: Use AskUserQuestion to get explicit approval before proceeding.

Ask:
- "Does this phase breakdown make sense for your project?"
- "Any concerns about the proposed approach?"
- "Should I proceed with creating the plan document?"

Only create plan document after user confirms approval.

### Step 5: Document Generation
1. Create `docs/plans/` directory if not exists
2. Generate plan document with all checkboxes unchecked
3. Add clear instructions in header about quality gates
4. Inform user of plan location and next steps

## Quality Gate Standards

Each phase MUST validate these items before proceeding to next phase:

**Build & Compilation**:
- [ ] Project builds/compiles without errors
- [ ] No syntax errors

**Test-Driven Development (TDD)**:
- [ ] Tests written BEFORE production code
- [ ] Red-Green-Refactor cycle followed
- [ ] Unit tests: ≥80% coverage for business logic
- [ ] Integration tests: Critical user flows validated
- [ ] Test suite runs in acceptable time (<5 minutes)

**Testing**:
- [ ] All existing tests pass
- [ ] New tests added for new functionality
- [ ] Test coverage maintained or improved

**Code Quality**:
- [ ] Linting passes with no errors
- [ ] Type checking passes (if applicable)
- [ ] Code formatting consistent

**Functionality**:
- [ ] Manual testing confirms feature works
- [ ] No regressions in existing functionality
- [ ] Edge cases tested

**Security & Performance**:
- [ ] No new security vulnerabilities
- [ ] No performance degradation
- [ ] Resource usage acceptable

**Documentation**:
- [ ] Code comments updated
- [ ] Documentation reflects changes

**Cleanup Standards**:
- [ ] **Pre-flight Cleanup**: Check for and terminate specific ports/processes (e.g., `lsof -i :5000`) BEFORE starting new servers.
- [ ] **Process Cleanup**: Terminate all background processes (servers, db tunnels) upon task completion.
- [ ] **Browser Cleanup**: Close all test browser tabs/windows after verification.
- [ ] **Resource Release**: Close file handlers and db connections.

## Progress Tracking Protocol

Add this to plan document header:

```markdown
**CRITICAL INSTRUCTIONS**: After completing each phase:
1. ✅ Check off completed task checkboxes
2. 🧪 Run all quality gate validation commands
3. ⚠️ Verify ALL quality gate items pass
4. 📅 Update "Last Updated" date
5. 📝 Document learnings in Notes section
6. ➡️ Only then proceed to next phase

⛔ DO NOT skip quality gates or proceed with failing checks
```

## Phase Sizing Guidelines

**Small Scope** (2-3 phases, 3-6 hours total):
- Single component or simple feature
- Minimal dependencies
- Clear requirements
- Example: Add dark mode toggle, create new form component

**Medium Scope** (4-5 phases, 8-15 hours total):
- Multiple components or moderate feature
- Some integration complexity
- Database changes or API work
- Example: User authentication system, search functionality

**Large Scope** (6-7 phases, 15-25 hours total):
- Complex feature spanning multiple areas
- Significant architectural impact
- Multiple integrations
- Example: AI-powered search with embeddings, real-time collaboration

## Risk Assessment

Identify and document:
- **Technical Risks**: API changes, performance issues, data migration
- **Dependency Risks**: External library updates, third-party service availability
- **Timeline Risks**: Complexity unknowns, blocking dependencies
- **Quality Risks**: Test coverage gaps, regression potential

For each risk, specify:
- Probability: Low/Medium/High
- Impact: Low/Medium/High
- Mitigation Strategy: Specific action steps

## Exception Handling Strategy

### 1. Custom Exceptions
Define domain-specific exceptions to handle expected error cases gracefully.
- Use `CoreException` as a base class if available.
- Include error codes and helpful messages.

### 2. Logging Standards
- **Error Level**: Unexpected system failures (stack trace required).
- **Warning Level**: Expected business logic failures (no stack trace).
- **Info Level**: High-level flow checkpoints.

### 3. User Feedback
- Errors exposed to users must be sanitized (no internal paths or raw SQL).
- Provide actionable next steps for the user.


## Rollback Strategy

For each phase, document how to revert changes if issues arise.
Consider:
- What code changes need to be undone
- Database migrations to reverse (if applicable)
- Configuration changes to restore
- Dependencies to remove

## Test Specification Guidelines

### Test-First Development Workflow

**For Each Feature Component**:
1. **Specify Test Cases** (before writing ANY code)
   - What inputs will be tested?
   - What outputs are expected?
   - What edge cases must be handled?
   - What error conditions should be tested?

2. **Write Tests** (Red Phase)
   - Write tests that WILL fail
   - Verify tests fail for the right reason
   - Run tests to confirm failure
   - Commit failing tests to track TDD compliance

3. **Implement Code** (Green Phase)
   - Write minimal code to make tests pass
   - Run tests frequently (every 2-5 minutes)
   - Stop when all tests pass
   - No additional functionality beyond tests

4. **Refactor** (Blue Phase)
   - Improve code quality while tests remain green
   - Extract duplicated logic
   - Improve naming and structure
   - Run tests after each refactoring step
   - Commit when refactoring complete

### Test Types

**Unit Tests**:
- **Target**: Individual functions, methods, classes
- **Dependencies**: None or mocked/stubbed
- **Speed**: Fast (<100ms per test)
- **Isolation**: Complete isolation from external systems
- **Coverage**: ≥80% of business logic

**Integration Tests**:
- **Target**: Interaction between components/modules
- **Dependencies**: May use real dependencies
- **Speed**: Moderate (<1s per test)
- **Isolation**: Tests component boundaries
- **Coverage**: Critical integration points

**End-to-End (E2E) Tests**:
- **Target**: Complete user workflows
- **Dependencies**: Real or near-real environment
- **Speed**: Slow (seconds to minutes)
- **Isolation**: Full system integration
- **Coverage**: Critical user journeys

**Agent Browser Testing**:
- **Tool**: `browser_subagent`
- **Purpose**: Verify UI rendering, layout, and basic interactivity
- **Requirement**: Mandatory for all UI-facing features
- **Artifact**: Must produce screenshots for `walkthrough.md`

### Test Coverage Calculation

**Coverage Thresholds** (adjust for your project):
- **Business Logic**: ≥90% (critical code paths)
- **Data Access Layer**: ≥80% (repositories, DAOs)
- **API/Controller Layer**: ≥70% (endpoints)
- **UI/Presentation**: Integration tests preferred over coverage

**Coverage Commands by Ecosystem**:
```bash
# JavaScript/TypeScript
jest --coverage
nyc report --reporter=html

# Python
pytest --cov=src --cov-report=html
coverage report

# Java
mvn jacoco:report
gradle jacocoTestReport

# Go
go test -cover ./...
go tool cover -html=coverage.out

# .NET
dotnet test /p:CollectCoverage=true /p:CoverageReporter=html
reportgenerator -reports:coverage.xml -targetdir:coverage

# Ruby
bundle exec rspec --coverage
open coverage/index.html

# PHP
phpunit --coverage-html coverage
```

### Common Test Patterns

**Arrange-Act-Assert (AAA) Pattern**:
```
test 'description of behavior':
  // Arrange: Set up test data and dependencies
  input = createTestData()

  // Act: Execute the behavior being tested
  result = systemUnderTest.method(input)

  // Assert: Verify expected outcome
  assert result == expectedOutput
```

**Given-When-Then (BDD Style)**:
```
test 'feature should behave in specific way':
  // Given: Initial context/state
  given userIsLoggedIn()

  // When: Action occurs
  when userClicksButton()

  // Then: Observable outcome
  then shouldSeeConfirmation()
```

**Mocking/Stubbing Dependencies**:
```
test 'component should call dependency':
  // Create mock/stub
  mockService = createMock(ExternalService)
  component = new Component(mockService)

  // Configure mock behavior
  when(mockService.method()).thenReturn(expectedData)

  // Execute and verify
  component.execute()
  verify(mockService.method()).calledOnce()
```

### Test Documentation in Plan

**In each phase, specify**:
1. **Test File Location**: Exact path where tests will be written
2. **Test Scenarios**: List of specific test cases
3. **Expected Failures**: What error should tests show initially?
4. **Exception Scenarios**: Specific error conditions to test (e.g., NetworkError, ValidationError)
5. **Coverage Target**: Percentage for this phase
5. **Dependencies to Mock**: What needs mocking/stubbing?
6. **Test Data**: What fixtures/factories are needed?

## Supporting Files Reference
- [plan-template.md](plan-template.md) - Complete plan document template

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lastdays03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
