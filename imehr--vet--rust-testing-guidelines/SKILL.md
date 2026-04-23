---
name: rust-testing-guidelines
description: Rust testing patterns with cargo test and mockall Use when this capability is needed.
metadata:
  author: imehr
---

# Rust Testing Guidelines

## Overview

This skill provides patterns for testing Rust applications with cargo test, including unit tests, integration tests, mocking with mockall, and async testing.

## Quick Reference

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Unit Test | Test single function | `#[test]` |
| Integration Test | Test modules together | `tests/` directory |
| Mock | Replace dependencies | `#[automock]` |
| Async Test | Test async code | `#[tokio::test]` |

## Project Structure

```
my_project/
├── src/
│   ├── lib.rs
│   └── services/
│       └── user_service.rs
├── tests/                    # Integration tests
│   ├── common/
│   │   └── mod.rs           # Shared test utilities
│   └── user_tests.rs
└── Cargo.toml
```

## Core Patterns

### Pattern 1: Unit Tests

```rust
// ✅ CORRECT: Unit tests in the same file
// src/services/user_service.rs

pub fn validate_email(email: &str) -> bool {
    email.contains('@') && email.contains('.')
}

pub fn hash_password(password: &str) -> String {
    // Implementation
    format!("hashed_{}", password)
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_validate_email_with_valid_email() {
        assert!(validate_email("user@example.com"));
    }

    #[test]
    fn test_validate_email_with_invalid_email() {
        assert!(!validate_email("invalid"));
        assert!(!validate_email("no-at-sign.com"));
        assert!(!validate_email("no-dot@com"));
    }

    #[test]
    fn test_hash_password_returns_hashed_value() {
        let result = hash_password("mypassword");
        assert!(result.starts_with("hashed_"));
        assert!(result.contains("mypassword"));
    }

    #[test]
    #[should_panic(expected = "empty password")]
    fn test_hash_password_panics_on_empty() {
        hash_password("");
    }
}
```

### Pattern 2: Async Tests

```rust
// ✅ CORRECT: Async test with tokio
use tokio;

#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_fetch_user_returns_user() {
        let pool = setup_test_db().await;
        let repo = UserRepository::new(pool);

        let user = repo.create("test@example.com", "Test").await.unwrap();
        let fetched = repo.find_by_id(user.id).await.unwrap();

        assert!(fetched.is_some());
        assert_eq!(fetched.unwrap().email, "test@example.com");
    }

    #[tokio::test]
    async fn test_fetch_user_returns_none_for_missing() {
        let pool = setup_test_db().await;
        let repo = UserRepository::new(pool);

        let result = repo.find_by_id(999).await.unwrap();
        assert!(result.is_none());
    }
}
```

### Pattern 3: Mocking with mockall

```rust
// ✅ CORRECT: Trait-based mocking
use mockall::{automock, predicate::*};
use async_trait::async_trait;

#[async_trait]
#[automock]
pub trait UserRepository {
    async fn find_by_id(&self, id: i64) -> Result<Option<User>, DbError>;
    async fn create(&self, email: &str, name: &str) -> Result<User, DbError>;
    async fn exists_by_email(&self, email: &str) -> Result<bool, DbError>;
}

pub struct UserService<R: UserRepository> {
    repo: R,
}

impl<R: UserRepository> UserService<R> {
    pub fn new(repo: R) -> Self {
        Self { repo }
    }

    pub async fn create_user(&self, email: &str, name: &str) -> Result<User, AppError> {
        if self.repo.exists_by_email(email).await? {
            return Err(AppError::Conflict("Email exists"));
        }
        let user = self.repo.create(email, name).await?;
        Ok(user)
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_create_user_success() {
        let mut mock_repo = MockUserRepository::new();

        mock_repo
            .expect_exists_by_email()
            .with(eq("new@example.com"))
            .times(1)
            .returning(|_| Ok(false));

        mock_repo
            .expect_create()
            .with(eq("new@example.com"), eq("New User"))
            .times(1)
            .returning(|email, name| {
                Ok(User {
                    id: 1,
                    email: email.to_string(),
                    name: name.to_string(),
                })
            });

        let service = UserService::new(mock_repo);
        let result = service.create_user("new@example.com", "New User").await;

        assert!(result.is_ok());
        let user = result.unwrap();
        assert_eq!(user.email, "new@example.com");
    }

    #[tokio::test]
    async fn test_create_user_duplicate_email() {
        let mut mock_repo = MockUserRepository::new();

        mock_repo
            .expect_exists_by_email()
            .returning(|_| Ok(true));

        let service = UserService::new(mock_repo);
        let result = service.create_user("existing@example.com", "Test").await;

        assert!(matches!(result, Err(AppError::Conflict(_))));
    }
}
```

### Pattern 4: Test Fixtures

```rust
// tests/common/mod.rs - Shared test utilities
use sqlx::PgPool;
use once_cell::sync::Lazy;
use tokio::sync::OnceCell;

static TEST_DB: OnceCell<PgPool> = OnceCell::const_new();

pub async fn get_test_pool() -> &'static PgPool {
    TEST_DB.get_or_init(|| async {
        let url = std::env::var("TEST_DATABASE_URL")
            .unwrap_or_else(|_| "postgres://test:test@localhost/test".to_string());
        PgPool::connect(&url).await.expect("Failed to connect")
    }).await
}

pub fn sample_user() -> User {
    User {
        id: 1,
        email: "test@example.com".to_string(),
        name: "Test User".to_string(),
        created_at: Utc::now(),
    }
}

pub struct TestContext {
    pub pool: PgPool,
}

impl TestContext {
    pub async fn new() -> Self {
        let pool = get_test_pool().await.clone();
        // Clean up before test
        sqlx::query!("DELETE FROM users").execute(&pool).await.unwrap();
        Self { pool }
    }
}
```

### Pattern 5: Integration Tests

```rust
// tests/user_tests.rs
mod common;

use common::TestContext;

#[tokio::test]
async fn test_user_crud_flow() {
    let ctx = TestContext::new().await;
    let repo = UserRepository::new(ctx.pool.clone());

    // Create
    let user = repo.create("test@example.com", "Test User").await.unwrap();
    assert_eq!(user.email, "test@example.com");

    // Read
    let fetched = repo.find_by_id(user.id).await.unwrap().unwrap();
    assert_eq!(fetched.name, "Test User");

    // Update
    let updated = repo.update(user.id, "Updated Name").await.unwrap().unwrap();
    assert_eq!(updated.name, "Updated Name");

    // Delete
    let deleted = repo.delete(user.id).await.unwrap();
    assert!(deleted);

    // Verify deleted
    let not_found = repo.find_by_id(user.id).await.unwrap();
    assert!(not_found.is_none());
}

#[tokio::test]
async fn test_api_create_user() {
    let ctx = TestContext::new().await;
    let app = create_test_app(ctx.pool.clone()).await;

    let response = app
        .oneshot(
            Request::builder()
                .method("POST")
                .uri("/api/users")
                .header("Content-Type", "application/json")
                .body(Body::from(r#"{"email":"new@test.com","name":"New"}"#))
                .unwrap(),
        )
        .await
        .unwrap();

    assert_eq!(response.status(), StatusCode::CREATED);
}
```

### Pattern 6: Property-Based Testing

```rust
// Using proptest for property-based testing
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_email_validation_never_panics(email in "\\PC*") {
        // Should never panic, regardless of input
        let _ = validate_email(&email);
    }

    #[test]
    fn test_valid_emails_accepted(
        local in "[a-z]{1,10}",
        domain in "[a-z]{1,10}",
        tld in "[a-z]{2,4}"
    ) {
        let email = format!("{}@{}.{}", local, domain, tld);
        assert!(validate_email(&email));
    }
}
```

## Test Attributes

```rust
#[test]                           // Basic test
#[tokio::test]                    // Async test
#[ignore]                         // Skip by default
#[should_panic]                   // Expect panic
#[should_panic(expected = "msg")] // Expect specific panic
```

## Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_name

# Run tests in specific module
cargo test module_name::

# Run with output
cargo test -- --nocapture

# Run ignored tests
cargo test -- --ignored

# Run specific integration test
cargo test --test user_tests
```

## Anti-Patterns

### Don't: Test Implementation Details

```rust
// ❌ BAD: Testing internal state
#[test]
fn test_internal_counter() {
    let service = MyService::new();
    service.do_something();
    assert_eq!(service.internal_counter, 1);  // Implementation detail!
}

// ✅ GOOD: Test observable behavior
#[test]
fn test_operation_result() {
    let service = MyService::new();
    let result = service.do_something();
    assert_eq!(result, expected_value);
}
```

### Don't: Hardcoded Sleep

```rust
// ❌ BAD: Arbitrary sleep
#[tokio::test]
async fn test_async_operation() {
    start_background_task();
    tokio::time::sleep(Duration::from_secs(5)).await;  // Slow!
    assert!(is_complete());
}

// ✅ GOOD: Wait for condition
#[tokio::test]
async fn test_async_operation() {
    start_background_task();
    tokio::time::timeout(Duration::from_secs(5), wait_for_completion())
        .await
        .expect("Timed out");
}
```

## Resources

| Topic | Link |
|-------|------|
| Unit Testing | [mdc:resources/unit-tests.md] |
| Integration Testing | [mdc:resources/integration-tests.md] |
| Mocking | [mdc:resources/mocking.md] |
| Fixtures | [mdc:resources/fixtures.md] |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
