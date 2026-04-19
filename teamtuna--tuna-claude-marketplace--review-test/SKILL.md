---
name: review-test
description: 테스트 커버리지 및 품질 검사. 변경된 코드의 테스트 존재 여부, 테스트 실행, 커버리지 리포트 생성. Use when this capability is needed.
metadata:
  author: teamtuna
---

# Test Review Skill

변경된 코드의 테스트 커버리지와 품질을 검사하는 skill입니다.

## When to Use

- PR 생성 전 테스트 검증
- 새 기능 개발 후 테스트 확인
- 테스트 커버리지 리포트 필요 시
- 테스트 누락 여부 확인

## Core Principles

1. **변경 코드 테스트**: 변경된 코드는 테스트 필수
2. **테스트 품질**: 의미 있는 assertions, edge case 포함
3. **빠른 피드백**: 로컬에서 빠르게 테스트 실행
4. **커버리지 목표**: 최소 70% 이상 권장

## Process

### 1. 변경 파일과 테스트 매핑

```bash
# 변경된 Kotlin 파일 확인
git diff main...HEAD --name-only | grep "\.kt$"

# 테스트 파일 제외한 소스 파일
git diff main...HEAD --name-only | grep "\.kt$" | grep -v "Test\.kt$"

# 각 소스 파일의 테스트 존재 여부 확인
for file in $(git diff main...HEAD --name-only | grep "\.kt$" | grep -v "Test"); do
    testFile="${file%.*}Test.kt"
    if [ -f "$testFile" ]; then
        echo "✅ $file -> $testFile"
    else
        echo "❌ $file -> 테스트 없음"
    fi
done
```

### 2. 테스트 실행

```bash
# 전체 단위 테스트
./gradlew test

# 특정 모듈 테스트
./gradlew :feature:home:test

# 특정 테스트 클래스 실행
./gradlew test --tests "com.example.UserViewModelTest"

# 특정 테스트 메서드 실행
./gradlew test --tests "com.example.UserViewModelTest.loadUser_success"

# 실패한 테스트만 재실행
./gradlew test --rerun-tasks

# 병렬 실행 (빠른 피드백)
./gradlew test --parallel
```

### 3. 커버리지 측정

```bash
# Jacoco 커버리지 리포트 생성
./gradlew jacocoTestReport

# 특정 모듈 커버리지
./gradlew :feature:home:jacocoTestReport

# 커버리지 검증 (threshold 미달 시 실패)
./gradlew jacocoTestCoverageVerification

# 리포트 위치
open build/reports/jacoco/test/html/index.html
```

### 4. 테스트 누락 검사

```bash
# public 함수 중 테스트 없는 것 찾기
# UseCase 파일
grep -l "class.*UseCase" --include="*.kt" -r . | while read f; do
    testFile="${f/main/test}"
    testFile="${testFile%.kt}Test.kt"
    if [ ! -f "$testFile" ]; then
        echo "❌ Missing test: $f"
    fi
done

# ViewModel 파일
grep -l "class.*ViewModel" --include="*.kt" -r . | while read f; do
    testFile="${f/main/test}"
    testFile="${testFile%.kt}Test.kt"
    if [ ! -f "$testFile" ]; then
        echo "❌ Missing test: $f"
    fi
done
```

## Checklist

### 🔴 Critical (테스트 필수)

#### 1. 비즈니스 로직 (UseCase)
```kotlin
// 소스: GetUserUseCase.kt
class GetUserUseCase(private val repository: UserRepository) {
    suspend operator fun invoke(userId: String): Result<User> {
        return repository.getUser(userId)
    }
}

// ✅ 테스트 필수: GetUserUseCaseTest.kt
class GetUserUseCaseTest {
    @Test
    fun `invoke returns user when repository succeeds`() = runTest {
        // Given
        val user = User(id = "1", name = "Test")
        coEvery { repository.getUser("1") } returns Result.success(user)

        // When
        val result = useCase("1")

        // Then
        assertTrue(result.isSuccess)
        assertEquals(user, result.getOrNull())
    }

    @Test
    fun `invoke returns failure when repository fails`() = runTest {
        // Given
        coEvery { repository.getUser("1") } returns Result.failure(Exception())

        // When
        val result = useCase("1")

        // Then
        assertTrue(result.isFailure)
    }
}
```

#### 2. ViewModel
```kotlin
// ✅ 테스트 필수: UserViewModelTest.kt
class UserViewModelTest {
    @Test
    fun `loadUser updates state to success`() = runTest {
        // Given
        val user = User(id = "1", name = "Test")
        coEvery { getUserUseCase("1") } returns Result.success(user)

        // When
        viewModel.loadUser("1")

        // Then
        assertEquals(UiState.Success(user), viewModel.state.value)
    }

    @Test
    fun `loadUser updates state to error on failure`() = runTest {
        // Given
        coEvery { getUserUseCase("1") } returns Result.failure(Exception("Network error"))

        // When
        viewModel.loadUser("1")

        // Then
        assertTrue(viewModel.state.value is UiState.Error)
    }
}
```

#### 3. Repository (데이터 변환 로직)
```kotlin
// ✅ 테스트 필수: UserRepositoryImplTest.kt
class UserRepositoryImplTest {
    @Test
    fun `getUser maps response to domain model`() = runTest {
        // Given
        val response = UserResponse(userId = "1", userName = "Test")
        coEvery { api.getUser("1") } returns response

        // When
        val result = repository.getUser("1")

        // Then
        assertEquals(User(id = "1", name = "Test"), result.getOrNull())
    }
}
```

### 🟡 Warnings

#### 4. Edge Case 누락
```kotlin
// ❌ Edge case 테스트 누락
class UserViewModelTest {
    @Test
    fun `loadUser success`() { ... }
    // 실패 케이스는?
    // 빈 데이터는?
    // 네트워크 오류는?
}

// ✅ Edge case 포함
class UserViewModelTest {
    @Test fun `loadUser success`() { ... }
    @Test fun `loadUser failure shows error`() { ... }
    @Test fun `loadUser empty list shows empty state`() { ... }
    @Test fun `loadUser network error shows retry`() { ... }
    @Test fun `loadUser while loading does nothing`() { ... }
}
```

#### 5. 테스트 이름 불명확
```kotlin
// ❌ 불명확한 이름
@Test fun test1() { ... }
@Test fun testLoadUser() { ... }

// ✅ 명확한 이름 (Given-When-Then)
@Test fun `loadUser returns success when api returns valid user`() { ... }
@Test fun `loadUser returns error when api throws exception`() { ... }
```

#### 6. Assertion 부족
```kotlin
// ❌ Assertion 없음 (항상 통과)
@Test
fun `loadUser test`() {
    viewModel.loadUser("1")
    // assertion 없음!
}

// ❌ 약한 Assertion
@Test
fun `loadUser test`() {
    viewModel.loadUser("1")
    assertNotNull(viewModel.state.value)  // 너무 약함
}

// ✅ 구체적인 Assertion
@Test
fun `loadUser updates state with user data`() {
    viewModel.loadUser("1")

    val state = viewModel.state.value
    assertIs<UiState.Success<User>>(state)
    assertEquals("1", state.data.id)
    assertEquals("Test User", state.data.name)
}
```

### 🟢 Suggestions

#### 7. Parameterized Test 활용
```kotlin
// ✅ 여러 입력값 테스트
@ParameterizedTest
@ValueSource(strings = ["", " ", "null"])
fun `validate rejects invalid input`(input: String) {
    val result = validator.validate(input)
    assertFalse(result.isValid)
}
```

#### 8. Test Fixture 분리
```kotlin
// ✅ 테스트 데이터 분리
object UserTestFixture {
    val validUser = User(id = "1", name = "Test")
    val emptyUser = User(id = "", name = "")

    fun createUser(
        id: String = "1",
        name: String = "Test"
    ) = User(id = id, name = name)
}
```

#### 9. Compose UI 테스트
```kotlin
// ✅ Compose 테스트
class UserScreenTest {
    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun `displays user name when loaded`() {
        composeTestRule.setContent {
            UserScreen(user = User(id = "1", name = "Test User"))
        }

        composeTestRule
            .onNodeWithText("Test User")
            .assertIsDisplayed()
    }
}
```

## Command Usage

```bash
# 변경된 파일의 테스트 존재 여부 확인
/review-test --missing

# 테스트 실행
/review-test --run

# 특정 모듈 테스트 실행
/review-test feature/home --run

# 커버리지 리포트 생성
/review-test --coverage

# 전체 검사 (누락 확인 + 실행 + 커버리지)
/review-test --all
```

## Output Format

```markdown
## 🧪 Test Review: [모듈/PR]

### 📊 요약
- 변경된 소스 파일: N개
- 테스트 존재: N개 ✅
- 테스트 누락: N개 ❌
- 테스트 통과율: N/N (100%)
- 라인 커버리지: N%

### ❌ 테스트 누락 파일
| 소스 파일 | 권장 테스트 |
|-----------|------------|
| GetUserUseCase.kt | GetUserUseCaseTest.kt |
| UserViewModel.kt | UserViewModelTest.kt |

### 🔴 실패한 테스트
- `UserViewModelTest.loadUser_failure` - AssertionError

### 🟡 커버리지 미달 (< 70%)
| 파일 | 커버리지 |
|------|---------|
| UserRepository.kt | 45% |

### ✅ 잘된 점
- 모든 UseCase에 테스트 존재
- Edge case 테스트 포함
- 테스트 네이밍 명확
```

## Test Structure Template

```
src/
├── main/kotlin/com/example/
│   ├── domain/
│   │   ├── model/User.kt
│   │   └── usecase/GetUserUseCase.kt
│   ├── data/
│   │   └── repository/UserRepositoryImpl.kt
│   └── presentation/
│       └── UserViewModel.kt
└── test/kotlin/com/example/
    ├── domain/
    │   └── usecase/GetUserUseCaseTest.kt     ← 필수
    ├── data/
    │   └── repository/UserRepositoryImplTest.kt  ← 필수
    └── presentation/
        └── UserViewModelTest.kt              ← 필수
```

## Coverage Thresholds

```kotlin
// build.gradle.kts
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = "0.70".toBigDecimal()  // 70% 최소
            }
        }
        rule {
            element = "CLASS"
            includes = listOf("*UseCase*", "*ViewModel*")
            limit {
                minimum = "0.80".toBigDecimal()  // 핵심 로직 80%
            }
        }
    }
}
```

## Notes

- 커버리지 숫자보다 테스트 품질이 중요
- 모든 public 함수는 테스트 고려
- Mocking은 외부 의존성만 (과도한 mocking 지양)
- CI에서 테스트 실패 시 머지 차단 권장
- Flaky 테스트는 즉시 수정 또는 제거

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teamtuna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
