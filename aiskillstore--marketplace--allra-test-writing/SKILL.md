---
name: allra-test-writing
description: Allra 백엔드 테스트 작성 표준. Use when writing test code, choosing test helpers, generating test data with Fixture Monkey, or verifying test coverage. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Allra Test Writing Standards

Allra 백엔드 팀의 테스트 작성 표준을 정의합니다. 테스트 헬퍼 선택, Fixture Monkey 데이터 생성, Given-When-Then 패턴, AssertJ 검증을 포함합니다.

## 프로젝트 기본 정보

이 가이드는 다음 환경을 기준으로 작성되었습니다:

- **Java**: 17 이상
- **Spring Boot**: 3.2 이상
- **Testing Framework**: JUnit 5
- **Assertion Library**: AssertJ
- **Mocking**: Mockito
- **Test Data**: Fixture Monkey (선택 사항)
- **Container**: Testcontainers (선택 사항)

**참고**: 프로젝트별로 사용하는 라이브러리나 버전이 다를 수 있습니다. 프로젝트에 맞게 조정하여 사용하세요.

## 테스트 헬퍼 선택 가이드

**주의**: 아래 테스트 헬퍼는 Allra 표준 템플릿에서 제공됩니다. 프로젝트에 이러한 헬퍼가 없는 경우, Spring Boot 기본 테스트 어노테이션(`@SpringBootTest`, `@DataJpaTest`, `@WebMvcTest` 등)을 직접 사용하되, 이 가이드의 테스트 패턴과 원칙은 동일하게 적용합니다.

| 헬퍼 | 태그 | 용도 | 무게 | 언제? |
|------|------|------|------|-------|
| **IntegrationTest** | Integration | 여러 서비스 통합 | 🔴 무거움 | 전체 워크플로우 |
| **RdbTest** | RDB | Repository, QueryDSL | 🟡 중간 | 쿼리 검증 |
| **ControllerTest** | Controller | API 엔드포인트 | 🟢 가벼움 | REST API 검증 |
| **RedisTest** | Redis | Redis 캐싱 | 🟢 가벼움 | 캐시 검증 |
| **MockingUnitTest** | MockingUnit | Service 단위 | 🟢 매우 가벼움 | 비즈니스 로직 |
| **PojoUnitTest** | PojoUnit | 도메인 로직 | 🟢 매우 가벼움 | 순수 자바 |

### 선택 플로우

```
API 엔드포인트? → ControllerTest
여러 서비스 통합? → IntegrationTest
Repository/QueryDSL? → RdbTest
Redis 캐싱? → RedisTest
Service 로직 (Mock)? → MockingUnitTest
도메인 로직 (POJO)? → PojoUnitTest
```

---

## 🎯 Mock vs Integration 선택 기준 (중요!)

**원칙**: 기본은 MockingUnitTest, 꼭 필요할 때만 IntegrationTest

**목표**: IntegrationTest 비율 5% 이하 유지

### 의사결정 플로우차트

```
┌─────────────────────────────────┐
│ 무엇을 테스트하려고 하는가?    │
└────────────┬────────────────────┘
             │
    ┌────────▼────────┐
    │ 도메인 로직만?  │ ──Yes──> PojoUnitTest
    └────────┬────────┘
             │ No
    ┌────────▼─────────────────────┐
    │ Repository/QueryDSL 쿼리?   │ ──Yes──> RdbTest
    └────────┬─────────────────────┘
             │ No
    ┌────────▼─────────────────────┐
    │ API 엔드포인트 응답/검증?   │ ──Yes──> ControllerTest
    └────────┬─────────────────────┘
             │ No
    ┌────────▼─────────────────────────────┐
    │ Service 비즈니스 로직 검증?         │
    └────────┬─────────────────────────────┘
             │
    ┌────────▼──────────────────────────────────────────┐
    │ 다음 중 하나라도 해당하는가?                      │
    │                                                   │
    │ 1. 💰 금전 처리 (입금/출금/이체/환불)            │
    │ 2. 🔄 트랜잭션 롤백이 중요한 워크플로우           │
    │ 3. 📊 여러 테이블 데이터 정합성 검증             │
    │ 4. 🔐 실제 DB 제약조건 검증 필수                 │
    │ 5. 📝 복잡한 상태 전이 (3단계 이상)              │
    │ 6. 🎯 이벤트 발행/리스너 통합 검증               │
    │ 7. 🤝 3개 이상 서비스 필수 협력                  │
    └────┬──────────────────────────────────────┬────────┘
         │ Yes                                  │ No
         │                                      │
    ┌────▼────────────┐              ┌─────────▼──────────┐
    │ IntegrationTest │              │ MockingUnitTest    │
    │ (최소화)        │              │ (기본 선택)       │
    └─────────────────┘              └────────────────────┘
```

### IntegrationTest가 필요한 구체적인 케이스

#### ✅ 1. 금전 처리 (입금/출금/이체/환불)

**이유**: 돈이 관련된 로직은 실제 DB 트랜잭션 동작 검증 필수

```java
// 예시: 펀딩 신청 (FsData → FsPayment → PointUsage → UserAccount 연계)
@DisplayName("펀딩 신청 시 금액 차감 및 결제 생성")
class ApplyServiceIntegrationTest extends IntegrationTest {

    @Test
    @Transactional
    void apply_DecreasesAmount_Success() {
        // given: 사용자 잔액 100만원
        User user = createUserWithBalance(1_000_000);

        // when: 50만원 펀딩 신청
        applyService.apply(new ApplyRequest(user.getId(), 500_000));

        // then: 실제 DB에서 잔액 50만원 확인
        User updated = userRepository.findById(user.getId()).get();
        assertThat(updated.getBalance()).isEqualTo(500_000);

        // then: FsPayment 생성 확인
        FsPayment payment = fsPaymentRepository.findByUserId(user.getId()).get();
        assertThat(payment.getAmount()).isEqualTo(500_000);
    }
}
```

#### ✅ 2. 트랜잭션 롤백이 중요한 워크플로우

**이유**: 실패 시 모든 작업이 원자적으로 롤백되어야 함

```java
// 예시: 결제 실패 시 전체 롤백
@Test
@DisplayName("결제 실패 시 신청 데이터도 롤백")
void apply_PaymentFails_RollbackAll() {
    // given
    User user = createUser();
    mockPaymentGateway_ToFail(); // 외부 결제는 Mock으로

    // when & then
    assertThatThrownBy(() -> applyService.apply(request))
        .isInstanceOf(PaymentException.class);

    // then: DB에 어떤 데이터도 저장되지 않음
    assertThat(fsDataRepository.findAll()).isEmpty();
    assertThat(fsPaymentRepository.findAll()).isEmpty();
}
```

**참고**: 외부 연동(결제 게이트웨이, 외부 API)은 `@MockBean`으로 처리

#### ✅ 3. 여러 테이블 데이터 정합성 검증

**이유**: 관련된 모든 테이블의 상태가 일관되게 유지되는지 확인

```java
// 예시: 계약 생성 시 UserAccount, Contract, FsData 모두 생성
@Test
@DisplayName("신규 계약 시 관련 테이블 모두 생성")
void createContract_CreatesAllRelatedData() {
    // when
    contractService.createContract(userId, contractType);

    // then: 3개 테이블 모두 데이터 존재
    assertThat(userAccountRepository.findByUserId(userId)).isPresent();
    assertThat(contractRepository.findByUserId(userId)).isPresent();
    assertThat(fsDataRepository.findByUserId(userId)).isPresent();
}
```

#### ✅ 4. 실제 DB 제약조건 검증

**이유**: Unique, FK, Check 제약조건은 실제 DB에서만 확인 가능

```java
// 예시: 중복 계좌 등록 방지
@Test
@DisplayName("동일 계좌번호 중복 등록 시 예외")
void registerAccount_Duplicate_ThrowsException() {
    // given
    userAccountRepository.save(new UserAccount(userId, "123-456-789"));

    // when & then: Unique 제약조건 위반
    assertThatThrownBy(() ->
        userAccountRepository.save(new UserAccount(userId, "123-456-789"))
    ).isInstanceOf(DataIntegrityViolationException.class);
}
```

#### ✅ 5. 복잡한 상태 전이 (3단계 이상)

**이유**: 상태 변화 흐름을 실제 시나리오대로 검증

```java
// 예시: 계약 상태 전이 (신청 → 심사 → 승인 → 완료)
@Test
@DisplayName("계약 워크플로우 전체 검증")
void contractWorkflow_FullCycle() {
    // given: 신청
    Contract contract = contractService.create(userId);
    assertThat(contract.getStatus()).isEqualTo(ContractStatus.PENDING);

    // when: 심사
    contractService.review(contract.getId());
    // then
    Contract reviewed = contractRepository.findById(contract.getId()).get();
    assertThat(reviewed.getStatus()).isEqualTo(ContractStatus.REVIEWED);

    // when: 승인
    contractService.approve(contract.getId());
    // then
    Contract approved = contractRepository.findById(contract.getId()).get();
    assertThat(approved.getStatus()).isEqualTo(ContractStatus.APPROVED);
}
```

#### ✅ 6. 이벤트 발행/리스너 통합 검증

**이유**: 이벤트가 실제로 발행되고 리스너가 동작하는지 확인

```java
// 예시: 계약 완료 이벤트 → 알림 발송
@Test
@DisplayName("계약 완료 시 알림 이벤트 발행")
void completeContract_PublishesEvent() {
    // given
    Contract contract = createContract(userId);

    // when
    contractService.complete(contract.getId());

    // then: 실제로 알림이 발송되었는가? (외부 알림은 @MockBean)
    verify(notificationService).sendContractCompleteNotification(userId);
}
```

#### ✅ 7. 3개 이상 서비스가 필수적으로 협력

**이유**: 서비스 간 상호작용을 실제 환경에서 검증

```java
// 예시: 주문 생성 → 재고 차감 → 결제 → 알림
@Test
@DisplayName("주문 생성 워크플로우")
void createOrder_FullWorkflow() {
    // given
    Product product = createProductWithStock(100);

    // when
    orderService.createOrder(userId, product.getId(), 10);

    // then: 재고 차감
    Product updated = productRepository.findById(product.getId()).get();
    assertThat(updated.getStock()).isEqualTo(90);

    // then: 결제 생성
    Payment payment = paymentRepository.findByUserId(userId).get();
    assertThat(payment.getStatus()).isEqualTo(PaymentStatus.COMPLETED);
}
```

### MockingUnitTest로 충분한 케이스

#### ✅ 대부분의 Service 로직

- 단순 조회 (findById, findAll)
- 데이터 변환/계산
- 검증 로직 (validation)
- 단일 엔티티 CRUD
- 비즈니스 규칙 검증

```java
// 예시: 할인율 계산 로직 (Mock으로 충분)
@ExtendWith(MockitoExtension.class)
class DiscountServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private DiscountService discountService;

    @Test
    @DisplayName("VIP 회원 10% 할인 계산")
    void calculateDiscount_VipUser_10Percent() {
        // given
        User vipUser = User.builder().grade("VIP").build();
        when(userRepository.findById(1L)).thenReturn(Optional.of(vipUser));

        // when
        BigDecimal discount = discountService.calculateDiscount(1L, new BigDecimal("10000"));

        // then
        assertThat(discount).isEqualByComparingTo(new BigDecimal("1000"));
    }
}
```

### 외부 연동 처리 원칙

**중요**: IntegrationTest에서도 외부 시스템은 `@MockBean`으로 처리

```java
@SpringBootTest
class PaymentServiceIntegrationTest extends IntegrationTest {

    @Autowired
    private PaymentService paymentService;

    @MockBean // 외부 결제 게이트웨이는 Mock
    private ExternalPaymentGateway externalPaymentGateway;

    @MockBean // 외부 알림 서비스는 Mock
    private ExternalNotificationService notificationService;

    @Test
    @DisplayName("결제 성공 시 내부 데이터 정합성 검증")
    void processPayment_Success() {
        // given: 외부 결제는 성공으로 Mock
        when(externalPaymentGateway.charge(any()))
            .thenReturn(new PaymentResult("SUCCESS", "tx-123"));

        // when: 실제 내부 로직 검증
        paymentService.processPayment(userId, amount);

        // then: 내부 DB 상태 확인
        Payment payment = paymentRepository.findByUserId(userId).get();
        assertThat(payment.getStatus()).isEqualTo(PaymentStatus.COMPLETED);
        assertThat(payment.getExternalTxId()).isEqualTo("tx-123");
    }
}
```

### 테스트 전략 요약

| 테스트 유형 | 목표 비율 | 실행 속도 | 주요 사용처 |
|------------|----------|----------|------------|
| **PojoUnitTest** | 30% | ⚡️ 0.01초 | 도메인 로직, 유틸리티 |
| **MockingUnitTest** | 50% | ⚡️ 0.1초 | Service 비즈니스 로직 |
| **ControllerTest** | 10% | 🟡 0.5초 | API 검증 |
| **RdbTest** | 5% | 🟡 1초 | 복잡한 쿼리 검증 |
| **IntegrationTest** | 5% | 🔴 5초 | 금전/트랜잭션/워크플로우 |

### 빠른 판단 체크리스트

새로운 테스트를 작성할 때 다음을 확인하세요:

```
□ 돈이 관련되어 있나요? (입금/출금/결제)
  → Yes: IntegrationTest

□ 실패 시 데이터 롤백이 중요한가요?
  → Yes: IntegrationTest

□ 3개 이상 테이블의 정합성을 확인해야 하나요?
  → Yes: IntegrationTest

□ DB 제약조건(Unique/FK)이 핵심인가요?
  → Yes: IntegrationTest

□ 복잡한 상태 전이(3단계+)를 검증하나요?
  → Yes: IntegrationTest

□ 이벤트 발행/리스너를 검증하나요?
  → Yes: IntegrationTest

□ 3개 이상 서비스가 협력하나요?
  → Yes: IntegrationTest

모두 No → MockingUnitTest 사용
```

---

## 테스트 헬퍼 구조

### IntegrationTest - 통합 테스트

```java
@Tag("Integration")
@SpringBootTest
public abstract class IntegrationTest {
    // 전체 Spring Context, Testcontainers 활용
}
```

**언제**: 여러 서비스 협력, 실제 DB/외부 시스템 필요
**주의**: 가장 무거움, 외부 API는 `@MockBean` 사용

### RdbTest - Repository 테스트

```java
@Tag("RDB")
@DataJpaTest
public abstract class RdbTest {}
```

**언제**: Repository CRUD, QueryDSL 쿼리, N+1 문제 검증

### ControllerTest - API 테스트

```java
@Tag("Controller")
@WebMvcTest(TargetController.class)
public abstract class ControllerTest {
    @Autowired
    protected MockMvc mockMvc;
}
```

**언제**: API 엔드포인트, HTTP Status, 입력 검증
**주의**: Service는 `@MockBean` 필수

### RedisTest - Redis 테스트

```java
@Tag("Redis")
@DataRedisTest
public abstract class RedisTest {}
```

**언제**: Redis 캐싱, 세션 저장소 검증

### MockingUnitTest - Service 단위 테스트

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;
}
```

**언제**: Service 로직 단위 테스트, 빠른 테스트
**주의**: Spring Context 없음, `@Autowired` 불가

### PojoUnitTest - 도메인 로직 테스트

```java
class UserTest {
    @Test
    void activate_Success() {
        // 순수 자바 로직 테스트
    }
}
```

**언제**: 도메인 엔티티, VO, 유틸리티 클래스

---

## Fixture Monkey - 테스트 데이터 생성

### 의존성 설정

```gradle
// Gradle
testImplementation 'com.navercorp.fixturemonkey:fixture-monkey-starter:1.0.13'
```

```xml
<!-- Maven -->
<dependency>
    <groupId>com.navercorp.fixturemonkey</groupId>
    <artifactId>fixture-monkey-starter</artifactId>
    <version>1.0.13</version>
    <scope>test</scope>
</dependency>
```

### 사용법

```java
import static {your.package}.fixture.FixtureFactory.FIXTURE_MONKEY;

// 단순 생성
User user = FIXTURE_MONKEY.giveMeOne(User.class);

// 특정 필드 지정
User user = FIXTURE_MONKEY.giveMeBuilder(User.class)
    .set("email", "test@example.com")
    .set("active", true)
    .sample();

// 여러 개 생성
List<User> users = FIXTURE_MONKEY.giveMe(User.class, 10);
```

---

## Given-When-Then 패턴 (필수)

**모든 테스트는 Given-When-Then 패턴 필수**

```java
@Test
@DisplayName("사용자 생성 - 성공")
void createUser_Success() {
    // given - 테스트 준비
    UserRequest request = new UserRequest("test@example.com", "password");
    User savedUser = FIXTURE_MONKEY.giveMeOne(User.class);
    when(userRepository.save(any())).thenReturn(savedUser);

    // when - 실제 실행
    UserResponse response = userService.createUser(request);

    // then - 검증
    assertThat(response).isNotNull();
    verify(userRepository, times(1)).save(any());
}
```

---

## AssertJ 검증 패턴

```java
// 단일 값
assertThat(response).isNotNull();
assertThat(response.userId()).isEqualTo(1L);

// 컬렉션
assertThat(users).hasSize(3);
assertThat(users).extracting(User::getEmail)
    .containsExactlyInAnyOrder("a@test.com", "b@test.com");

// Boolean
assertThat(user.isActive()).isTrue();

// 예외
assertThatThrownBy(() -> userService.findById(999L))
    .isInstanceOf(BusinessException.class)
    .hasMessageContaining("USER_NOT_FOUND");

// Optional
assertThat(result).isPresent();
assertThat(result.get().getName()).isEqualTo("홍길동");
```

---

## Mockito 패턴

### Mock 설정

```java
// 반환값
when(userRepository.findById(1L)).thenReturn(Optional.of(user));

// void 메서드
doNothing().when(emailService).sendEmail(any());

// 예외 발생
when(userRepository.findById(999L))
    .thenThrow(new BusinessException(ErrorCode.USER_NOT_FOUND));
```

### Mock 호출 검증

```java
// 호출 횟수
verify(userRepository, times(1)).findById(1L);
verify(userRepository, never()).delete(any());

// 인자 검증
verify(userRepository).save(argThat(user ->
    user.getEmail().equals("test@example.com")
));
```

---

## 테스트 명명 규칙

### 클래스

```java
class ApplyServiceIntegrationTest extends IntegrationTest  // Integration
class UserRepositoryTest extends RdbTest                   // Repository
class UserControllerTest extends ControllerTest            // Controller
class UserServiceTest                                      // Service Unit
class UserTest                                             // Domain
```

### 메서드

```java
// 패턴: {메서드명}_{시나리오}_{예상결과}
@Test
@DisplayName("사용자 생성 - 성공")
void createUser_ValidRequest_Success()

@Test
@DisplayName("사용자 조회 - 사용자 없음")
void findById_UserNotFound_ThrowsException()
```

---

## 테스트 예시

### Controller 테스트

```java
@DisplayName("User -> UserController 테스트")
@WebMvcTest(UserController.class)
class UserControllerTest extends ControllerTest {

    @MockBean
    private UserService userService;

    @Test
    @DisplayName("사용자 조회 API - 성공")
    void getUser_Success() throws Exception {
        // given
        Long userId = 1L;
        UserResponse response = new UserResponse(userId, "test@example.com");
        when(userService.findById(userId)).thenReturn(response);

        // when & then
        mockMvc.perform(get("/api/v1/users/{id}", userId))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.userId").value(userId));
    }
}
```

### Service 단위 테스트

```java
@ExtendWith(MockitoExtension.class)
@DisplayName("User -> UserService 단위 테스트")
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    @DisplayName("사용자 조회 - 성공")
    void findById_Success() {
        // given
        Long userId = 1L;
        User user = FIXTURE_MONKEY.giveMeBuilder(User.class)
            .set("id", userId)
            .sample();
        when(userRepository.findById(userId)).thenReturn(Optional.of(user));

        // when
        UserResponse response = userService.findById(userId);

        // then
        assertThat(response).isNotNull();
        assertThat(response.userId()).isEqualTo(userId);
        verify(userRepository, times(1)).findById(userId);
    }
}
```

### Repository 테스트

```java
@DisplayName("User -> UserRepository 테스트")
class UserRepositoryTest extends RdbTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    @DisplayName("활성 사용자 조회 - 성공")
    void findActiveUsers_Success() {
        // given
        User active = FIXTURE_MONKEY.giveMeBuilder(User.class)
            .set("active", true)
            .sample();
        userRepository.save(active);

        // when
        List<UserDto> result = userRepository.findActiveUsers();

        // then
        assertThat(result).hasSize(1);
        assertThat(result).extracting(UserDto::email)
            .contains(active.getEmail());
    }
}
```

---

## When to Use This Skill

이 skill은 다음 상황에서 자동으로 적용됩니다:

- 테스트 파일 생성 또는 수정
- **테스트 헬퍼 선택 (IntegrationTest vs MockingUnitTest 판단)**
- 테스트 데이터 생성 (Fixture Monkey 사용)
- Given-When-Then 패턴 적용
- AssertJ 검증 코드 작성
- Mockito Mock 설정 및 검증

**특히 중요**: 새로운 Service 테스트 작성 시 먼저 "Mock vs Integration 선택 기준"을 확인하세요!

---

## Checklist

테스트 코드 작성 시 확인사항:

**모든 테스트 공통**
- [ ] Given-When-Then 패턴을 따르는가?
- [ ] @DisplayName으로 테스트 의도가 명확한가?
- [ ] AssertJ로 검증하는가?
- [ ] 메서드명이 `메서드_시나리오_결과` 패턴인가?

**테스트 헬퍼 선택 (가장 먼저 확인!)**
- [ ] 금전 처리(입금/출금/결제) 또는 트랜잭션 롤백 검증이 필요한가? → IntegrationTest
- [ ] 3개 이상 테이블 정합성 또는 DB 제약조건 검증이 필요한가? → IntegrationTest
- [ ] 복잡한 상태 전이(3단계+) 또는 이벤트 발행/리스너 검증이 필요한가? → IntegrationTest
- [ ] 3개 이상 서비스가 협력하는가? → IntegrationTest
- [ ] 위 조건 모두 해당 안됨 → MockingUnitTest 사용

**IntegrationTest**
- [ ] 위 선택 기준 중 하나 이상에 해당하는가?
- [ ] 외부 API는 @MockBean으로 처리했는가?
- [ ] 정말 IntegrationTest가 필요한지 다시 한번 검토했는가?

**RdbTest**
- [ ] Repository/QueryDSL 테스트만 포함하는가?
- [ ] N+1 문제를 검증했는가?

**ControllerTest**
- [ ] @WebMvcTest(TargetController.class)를 명시했는가?
- [ ] Service는 @MockBean으로 처리했는가?
- [ ] HTTP Status Code를 검증하는가?

**MockingUnitTest**
- [ ] @Mock으로 의존성, @InjectMocks로 테스트 대상을 주입했는가?
- [ ] verify()로 Mock 호출을 검증했는가?

**PojoUnitTest**
- [ ] 도메인 로직만 테스트하는가?
- [ ] 외부 의존성이 없는가?

---

## 테스트 실행 명령어

### Gradle

```bash
./gradlew test                                    # 전체 테스트
./gradlew test --tests * -Dtest.tags=Integration # 태그별 실행
./gradlew test --tests UserServiceTest            # 특정 클래스
```

### Maven

```bash
./mvnw test                        # 전체 테스트
./mvnw test -Dgroups=Integration   # 태그별 실행
./mvnw test -Dtest=UserServiceTest # 특정 클래스
```

---

## 테스트 품질 기준

1. **커버리지**: 핵심 비즈니스 로직 70% 이상
2. **격리성**: 각 테스트가 독립적으로 실행 가능
3. **속도**: 단위 테스트 1초 이내, 통합 테스트 5초 이내
4. **명확성**: 테스트 이름만으로 의도 파악 가능
5. **신뢰성**: 같은 입력에 항상 같은 결과

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
