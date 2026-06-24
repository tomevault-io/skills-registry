---
name: server-tdd
description: server 모듈에 TDD(Test-Driven Development) 방식으로 기능을 구현합니다. 새로운 기능 개발, 버그 수정, 리팩토링 시 사용합니다. Use when this capability is needed.
metadata:
  author: robinjoon
---

# TDD (Test-Driven Development) 스킬

$ARGUMENTS 기능을 TDD 방식으로 구현합니다.

## TDD 사이클 (Red-Green-Refactor)

반드시 아래 순서를 따라야 합니다:

### 1. Red: 실패하는 테스트 작성
- 구현하려는 기능의 테스트를 **먼저** 작성합니다
- 테스트 파일 위치: `src/test/kotlin/` 하위
- Kotest FunSpec 스타일 사용
- 테스트 실행하여 **실패 확인** (이 단계에서 실패해야 정상)

```bash
./gradlew test --tests "패키지.테스트클래스"
```

### 2. Green: 최소한의 코드로 테스트 통과
- 테스트를 통과시키기 위한 **최소한의** 코드만 작성
- 완벽한 코드가 아니어도 됨
- 테스트 실행하여 **성공 확인**

### 3. Refactor: 코드 개선
- 테스트가 통과하는 상태를 유지하면서 코드 개선
- 중복 제거, 네이밍 개선, 구조 개선
- 리팩토링 후 테스트 재실행하여 **여전히 성공 확인**

## 필수 규칙

1. **테스트 없이 프로덕션 코드 작성 금지**
   - 모든 새로운 기능은 테스트가 먼저 존재해야 함

2. **한 번에 하나의 테스트만**
   - 여러 테스트를 한꺼번에 작성하지 않음
   - 하나의 테스트 → 구현 → 다음 테스트

3. **테스트 실행 필수**
   - 각 단계에서 반드시 테스트를 실행하고 결과 확인
   - Red 단계: 실패 확인
   - Green/Refactor 단계: 성공 확인

4. **작은 단위로 진행**
   - 큰 기능은 작은 테스트 케이스로 분해
   - 점진적으로 기능 완성

## 테스트 작성 가이드

### Kotest FunSpec 예시

```kotlin
@SpringBootTest
@EnableAutoConfiguration(exclude = [DataSourceAutoConfiguration::class])
class MyFeatureTest : FunSpec({
    extensions(listOf(SpringExtension()))
}) {
    init {
        test("기능 설명") {
            // Given
            val input = ...

            // When
            val result = ...

            // Then
            result shouldBe expected
        }
    }
}
```

### 단위 테스트 (Spring 없이)

```kotlin
class MyServiceTest : FunSpec({
    test("기능 설명") {
        // Given
        val service = MyService()

        // When
        val result = service.doSomething()

        // Then
        result shouldBe expected
    }
})
```

## 주의사항

### Kotest + Spring 통합 테스트 제한

프로젝트의 `ProjectConfig`가 `SpringTestLifecycleMode.Root`와 `InstancePerRoot`를 사용하므로, `@Autowired` 필드가 `init` 블록에서 초기화되지 않습니다.

**해결책**: Controller 등의 테스트는 **Spring을 배제한 순수 단위 테스트**로 작성합니다:

```kotlin
class PostControllerTest : FunSpec({
    context("POST /api/posts") {
        test("게시글을 생성할 수 있다") {
            // Given - 직접 인스턴스화, MockK로 의존성 모킹
            val createPostUseCase = mockk<CreatePostUseCase>()
            val controller = PostController(createPostUseCase)

            every { createPostUseCase.execute(any()) } returns PostResult(
                id = 1L,
                title = "테스트",
                content = "내용",
                authorId = 1L,
                createdAt = Instant.now()
            )

            // When
            val response = controller.create(CreatePostRequest("테스트", "내용", 1L))

            // Then
            response.id shouldBe 1L
            verify { createPostUseCase.execute(any()) }
        }
    }
})
```

> **중요**: `ProjectConfig` 등 프로젝트 전역 설정을 임의로 수정하지 마세요.

## 진행 보고

각 TDD 사이클마다 다음을 보고합니다:
- 현재 단계 (Red/Green/Refactor)
- 테스트 실행 결과
- 다음 단계 계획

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robinjoon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
