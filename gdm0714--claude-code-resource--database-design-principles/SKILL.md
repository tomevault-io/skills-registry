---
name: database-design-principles
description: 데이터베이스 설계 원칙 - 확장 가능하고 유지보수 쉬운 스키마 만들기 Use when this capability is needed.
metadata:
  author: gdm0714
---

# 데이터베이스 설계 원칙

## 핵심 철학

> "데이터는 영원하지만, 코드는 바뀐다. 데이터 구조를 신중하게 설계하라."

### 1. 정규화부터 시작, 필요 시 비정규화
- 3차 정규화(3NF)까지는 기본
- 성능 문제가 실제로 발생하면 그때 비정규화

### 2. 미래를 예측하지 말고, 변화에 대비하라
- 확장 가능한 구조
- 하지만 과도한 추상화는 금물

### 3. 제약 조건은 DB 레벨에서
- 애플리케이션 코드는 바뀌어도 DB는 데이터 무결성 보장

---

## 명명 규칙

### 일관된 네이밍

```sql
-- ✅ 좋은 이름
users                    -- 복수형 테이블명
user_id                  -- 스네이크 케이스
created_at, updated_at   -- 표준 타임스탬프
is_active, has_verified  -- boolean은 is/has 접두사

-- ❌ 나쁜 이름
tblUser, UserTable       -- 접두사/접미사 불필요
userId, createdAt        -- 카멜케이스 (DB에서는 비추천)
active                   -- boolean 불명확
date                     -- 너무 일반적
```

### 관계 테이블 명명

```sql
-- ✅ 좋은 이름
user_roles              -- 알파벳 순
post_tags
order_items

-- ❌ 나쁜 이름
roles_users             -- 순서 일관성 없음
posttag                 -- 언더스코어 없음
order_item              -- 단수형 (복수형 권장)
```

---

## 기본 설계 패턴

### 패턴 1: 타임스탬프는 필수

```sql
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(100) NOT NULL,

  -- ✅ 필수 타임스탬프
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),

  -- 🤔 상황에 따라 추가
  deleted_at TIMESTAMP  -- Soft delete
);

-- updated_at 자동 갱신 (PostgreSQL)
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

### 패턴 2: Soft Delete

```sql
-- ✅ Soft Delete 패턴
CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  title VARCHAR(200) NOT NULL,
  content TEXT,
  user_id BIGINT NOT NULL REFERENCES users(id),

  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
  deleted_at TIMESTAMP,  -- NULL이면 활성, 값이 있으면 삭제됨

  -- 삭제되지 않은 것만 조회
  CONSTRAINT check_not_deleted CHECK (deleted_at IS NULL)
);

-- 인덱스 (삭제되지 않은 것만)
CREATE INDEX idx_posts_not_deleted ON posts (user_id)
  WHERE deleted_at IS NULL;

-- 조회 (deleted_at이 NULL인 것만)
SELECT * FROM posts WHERE deleted_at IS NULL;
```

### 패턴 3: UUID vs Auto Increment

```sql
-- 자동 증가 ID (기본 권장)
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,  -- ✅ 간단하고 빠름
  email VARCHAR(255) NOT NULL
);

-- UUID (분산 시스템, 보안)
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE sessions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),  -- ✅ 예측 불가능
  user_id BIGINT NOT NULL,
  token TEXT NOT NULL,
  expires_at TIMESTAMP NOT NULL
);
```

---

## 정규화

### 1차 정규화 (1NF): 원자값

```sql
-- ❌ 반정규형 (배열)
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100),
  phone_numbers TEXT  -- "010-1234-5678, 010-9876-5432" ❌
);

-- ✅ 정규화 (별도 테이블)
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE user_phones (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id),
  phone_number VARCHAR(20) NOT NULL,
  type VARCHAR(20)  -- 'mobile', 'home', 'work'
);
```

### 2차 정규화 (2NF): 부분 종속 제거

```sql
-- ❌ 부분 종속
CREATE TABLE order_items (
  order_id BIGINT,
  product_id BIGINT,
  product_name VARCHAR(200),     -- product_id에만 종속
  product_price DECIMAL(10, 2),  -- product_id에만 종속
  quantity INT,
  PRIMARY KEY (order_id, product_id)
);

-- ✅ 부분 종속 제거
CREATE TABLE products (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  price DECIMAL(10, 2) NOT NULL
);

CREATE TABLE order_items (
  id BIGSERIAL PRIMARY KEY,
  order_id BIGINT NOT NULL REFERENCES orders(id),
  product_id BIGINT NOT NULL REFERENCES products(id),
  quantity INT NOT NULL,
  unit_price DECIMAL(10, 2) NOT NULL,  -- 주문 시점의 가격 (중요!)
  UNIQUE (order_id, product_id)
);
```

### 3차 정규화 (3NF): 이행 종속 제거

```sql
-- ❌ 이행 종속
CREATE TABLE employees (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100),
  department_name VARCHAR(100),
  department_location VARCHAR(200)  -- department_name을 통해 이행 종속
);

-- ✅ 이행 종속 제거
CREATE TABLE departments (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE,
  location VARCHAR(200)
);

CREATE TABLE employees (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  department_id BIGINT NOT NULL REFERENCES departments(id)
);
```

---

## 관계 설계

### 1:N 관계

```sql
-- 사용자 1명 : 주문 N개
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id),  -- FK
  total_amount DECIMAL(10, 2) NOT NULL,
  status VARCHAR(20) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- 인덱스 (FK에는 거의 항상 인덱스 필요)
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

### N:M 관계

```sql
-- 학생 N명 : 과목 M개
CREATE TABLE students (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  student_number VARCHAR(20) NOT NULL UNIQUE
);

CREATE TABLE courses (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(200) NOT NULL,
  code VARCHAR(20) NOT NULL UNIQUE
);

-- 중간 테이블
CREATE TABLE enrollments (
  id BIGSERIAL PRIMARY KEY,
  student_id BIGINT NOT NULL REFERENCES students(id),
  course_id BIGINT NOT NULL REFERENCES courses(id),
  enrolled_at TIMESTAMP NOT NULL DEFAULT NOW(),
  grade VARCHAR(2),  -- 추가 정보

  UNIQUE (student_id, course_id)  -- 중복 방지
);

CREATE INDEX idx_enrollments_student ON enrollments(student_id);
CREATE INDEX idx_enrollments_course ON enrollments(course_id);
```

---

## 인덱스 전략

### 인덱스가 필요한 곳

```sql
-- ✅ 인덱스가 필요한 컬럼
-- 1. Primary Key (자동)
-- 2. Foreign Key
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 3. 자주 검색하는 컬럼
CREATE INDEX idx_users_email ON users(email);

-- 4. 정렬에 사용하는 컬럼
CREATE INDEX idx_posts_created_at ON posts(created_at DESC);

-- 5. 복합 인덱스 (순서 중요!)
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
-- WHERE user_id = ? AND status = ? ✅ 사용
-- WHERE status = ? ❌ 사용 안 됨 (첫 컬럼이 빠짐)

-- 6. 부분 인덱스
CREATE INDEX idx_orders_pending ON orders(created_at)
  WHERE status = 'PENDING';  -- PENDING 주문만 인덱스
```

### ❌ 불필요한 인덱스

```sql
-- 작은 테이블 (< 1000 rows)
-- 거의 사용하지 않는 컬럼
-- 데이터 분포가 좋지 않은 컬럼 (예: boolean)
CREATE INDEX idx_users_is_active ON users(is_active);  -- ❌
-- is_active가 대부분 true라면 인덱스 효과 없음
```

---

## 제약 조건

### NOT NULL vs NULL

```sql
CREATE TABLE products (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(200) NOT NULL,        -- ✅ 필수
  description TEXT,                   -- 🤔 선택 (NULL 허용)
  price DECIMAL(10, 2) NOT NULL,     -- ✅ 필수
  discount_price DECIMAL(10, 2),     -- 🤔 선택

  -- ✅ CHECK 제약조건
  CONSTRAINT check_price_positive CHECK (price > 0),
  CONSTRAINT check_discount_valid
    CHECK (discount_price IS NULL OR discount_price < price)
);
```

### UNIQUE 제약조건

```sql
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,           -- ✅ 단일 UNIQUE
  phone VARCHAR(20),

  -- ✅ 복합 UNIQUE
  UNIQUE (email, phone)
);

-- 부분 UNIQUE (NULL 제외)
CREATE UNIQUE INDEX idx_users_phone ON users(phone)
  WHERE phone IS NOT NULL;
```

### Foreign Key 옵션

```sql
CREATE TABLE comments (
  id BIGSERIAL PRIMARY KEY,
  post_id BIGINT NOT NULL,
  user_id BIGINT NOT NULL,
  content TEXT NOT NULL,

  -- ✅ CASCADE: 부모 삭제 시 자식도 삭제
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE,

  -- ✅ SET NULL: 부모 삭제 시 NULL로
  FOREIGN KEY (user_id) REFERENCES users(id)
    ON DELETE SET NULL
);

-- ON DELETE 옵션:
-- CASCADE: 부모 삭제 → 자식도 삭제
-- SET NULL: 부모 삭제 → 자식의 FK를 NULL로
-- RESTRICT: 자식이 있으면 부모 삭제 불가 (기본값)
-- NO ACTION: RESTRICT와 비슷
```

---

## 한국 서비스 특화

### 주소 테이블

```sql
CREATE TABLE addresses (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id),

  -- 도로명주소
  postal_code VARCHAR(5) NOT NULL,              -- 우편번호
  address VARCHAR(200) NOT NULL,                 -- 도로명주소
  address_detail VARCHAR(100),                   -- 상세주소

  -- 지번주소 (선택)
  jibun_address VARCHAR(200),

  -- 좌표
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),

  -- 배송 정보
  recipient_name VARCHAR(100) NOT NULL,
  recipient_phone VARCHAR(20) NOT NULL,
  is_default BOOLEAN NOT NULL DEFAULT false,

  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- 사용자당 기본 배송지는 하나만
CREATE UNIQUE INDEX idx_addresses_default ON addresses(user_id)
  WHERE is_default = true;
```

### 주문 테이블

```sql
CREATE TABLE orders (
  id BIGSERIAL PRIMARY KEY,
  order_number VARCHAR(20) NOT NULL UNIQUE,      -- 주문번호
  user_id BIGINT NOT NULL REFERENCES users(id),

  -- 금액
  product_amount DECIMAL(10, 2) NOT NULL,        -- 상품 금액
  discount_amount DECIMAL(10, 2) NOT NULL DEFAULT 0,  -- 할인
  delivery_fee DECIMAL(10, 2) NOT NULL DEFAULT 0,     -- 배송비
  total_amount DECIMAL(10, 2) NOT NULL,          -- 최종 금액

  -- 상태
  status VARCHAR(20) NOT NULL DEFAULT 'PENDING', -- 주문상태

  -- 배송 정보
  recipient_name VARCHAR(100) NOT NULL,
  recipient_phone VARCHAR(20) NOT NULL,
  delivery_address TEXT NOT NULL,
  delivery_request TEXT,                         -- 배송 요청사항

  -- 결제 정보
  payment_method VARCHAR(20),                    -- 결제 수단
  paid_at TIMESTAMP,                             -- 결제 시각

  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),

  -- 체크 제약
  CONSTRAINT check_amounts CHECK (
    total_amount = product_amount - discount_amount + delivery_fee
  )
);

-- 주문 상품
CREATE TABLE order_items (
  id BIGSERIAL PRIMARY KEY,
  order_id BIGINT NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id BIGINT NOT NULL REFERENCES products(id),

  -- 주문 시점의 정보 저장 (중요!)
  product_name VARCHAR(200) NOT NULL,
  product_price DECIMAL(10, 2) NOT NULL,

  quantity INT NOT NULL CHECK (quantity > 0),
  subtotal DECIMAL(10, 2) NOT NULL,

  created_at TIMESTAMP NOT NULL DEFAULT NOW(),

  UNIQUE (order_id, product_id)
);
```

---

## 성능 최적화

### 비정규화 (신중하게)

```sql
-- ✅ 읽기 성능이 중요한 경우
CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  title VARCHAR(200) NOT NULL,
  content TEXT,
  user_id BIGINT NOT NULL REFERENCES users(id),

  -- 비정규화: 캐시 컬럼
  comment_count INT NOT NULL DEFAULT 0,          -- 댓글 수
  like_count INT NOT NULL DEFAULT 0,             -- 좋아요 수
  view_count INT NOT NULL DEFAULT 0,             -- 조회 수

  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

-- 트리거로 자동 업데이트
CREATE OR REPLACE FUNCTION update_post_comment_count()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'INSERT' THEN
    UPDATE posts SET comment_count = comment_count + 1
      WHERE id = NEW.post_id;
  ELSIF TG_OP = 'DELETE' THEN
    UPDATE posts SET comment_count = comment_count - 1
      WHERE id = OLD.post_id;
  END IF;
  RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_comment_count
  AFTER INSERT OR DELETE ON comments
  FOR EACH ROW
  EXECUTE FUNCTION update_post_comment_count();
```

### 파티셔닝

```sql
-- 날짜별 파티셔닝 (로그 테이블 등)
CREATE TABLE logs (
  id BIGSERIAL,
  user_id BIGINT,
  action VARCHAR(100),
  created_at TIMESTAMP NOT NULL,
  PRIMARY KEY (id, created_at)
) PARTITION BY RANGE (created_at);

-- 월별 파티션
CREATE TABLE logs_2024_01 PARTITION OF logs
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

CREATE TABLE logs_2024_02 PARTITION OF logs
  FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');
```

---

## 마이그레이션 전략

### 안전한 컬럼 추가

```sql
-- ✅ 기본값이 있는 컬럼 (안전)
ALTER TABLE users
  ADD COLUMN phone VARCHAR(20) DEFAULT '';

-- ⚠️ NOT NULL + 기본값 없음 (위험)
-- 대용량 테이블에서는 오래 걸림
ALTER TABLE users
  ADD COLUMN phone VARCHAR(20) NOT NULL;  -- ❌

-- ✅ 단계별 추가 (안전)
-- 1. NULL 허용으로 추가
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- 2. 데이터 채우기
UPDATE users SET phone = '' WHERE phone IS NULL;

-- 3. NOT NULL 제약조건
ALTER TABLE users ALTER COLUMN phone SET NOT NULL;
```

---

## 체크리스트

### 테이블 설계 전

- [ ] 테이블 이름이 복수형인가?
- [ ] created_at, updated_at이 있는가?
- [ ] Primary Key가 정의되어 있는가?
- [ ] Foreign Key에 인덱스가 있는가?
- [ ] NOT NULL이 적절히 사용되었는가?
- [ ] 제약 조건이 DB 레벨에 있는가?

### 배포 전

- [ ] 마이그레이션 스크립트가 롤백 가능한가?
- [ ] 대용량 테이블의 ALTER는 피했는가?
- [ ] 인덱스가 과도하지 않은가?
- [ ] 정규화가 적절한가?

---

## 마무리 원칙

> "지금 당장 필요한 것만 만들되, 미래를 위한 여지는 남겨두라"

- **단순함**: 복잡한 것보다 단순한 것이 낫다
- **일관성**: 명명 규칙을 지켜라
- **무결성**: 제약 조건으로 데이터를 보호하라
- **성능**: 실제 문제가 발생하면 그때 최적화하라
- **문서화**: 중요한 비즈니스 로직은 주석으로 남겨라

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdm0714) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
