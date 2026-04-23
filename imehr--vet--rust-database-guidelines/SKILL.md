---
name: rust-database-guidelines
description: SQLx and Diesel database patterns Use when this capability is needed.
metadata:
  author: imehr
---

# Rust Database Guidelines

## Overview

This skill provides patterns for database operations in Rust using SQLx (async, compile-time checked) or Diesel (sync, type-safe ORM). Use this when working with databases in Rust projects.

## Quick Reference

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Query | Fetch data | `sqlx::query_as!` |
| Transaction | Multiple operations | `pool.begin()` |
| Migration | Schema changes | `sqlx migrate run` |
| Repository | Data access layer | `UserRepository` |

## SQLx vs Diesel

| Feature | SQLx | Diesel |
|---------|------|--------|
| Async | Yes | No (use `tokio_diesel`) |
| Compile-time checks | Yes (with macros) | Yes |
| ORM features | Minimal | Full ORM |
| Query style | SQL strings | DSL |
| Learning curve | Lower | Higher |

## Core Patterns (SQLx)

### Pattern 1: Connection Pool

```rust
// ✅ CORRECT: Connection pool setup
use sqlx::postgres::PgPoolOptions;
use sqlx::PgPool;
use std::time::Duration;

pub async fn create_pool(database_url: &str) -> Result<PgPool, sqlx::Error> {
    PgPoolOptions::new()
        .max_connections(10)
        .min_connections(2)
        .acquire_timeout(Duration::from_secs(3))
        .idle_timeout(Duration::from_secs(600))
        .connect(database_url)
        .await
}

// In main.rs
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    dotenvy::dotenv().ok();
    let database_url = std::env::var("DATABASE_URL")?;
    let pool = create_pool(&database_url).await?;

    // Run migrations
    sqlx::migrate!("./migrations").run(&pool).await?;

    // Start server with pool
    Ok(())
}
```

### Pattern 2: Model Definition

```rust
// ✅ CORRECT: SQLx model with FromRow
use chrono::{DateTime, Utc};
use sqlx::FromRow;
use serde::{Deserialize, Serialize};

#[derive(Debug, Clone, FromRow, Serialize)]
pub struct User {
    pub id: i64,
    pub email: String,
    pub name: String,
    #[serde(skip_serializing)]
    pub password_hash: String,
    pub is_active: bool,
    pub created_at: DateTime<Utc>,
    pub updated_at: DateTime<Utc>,
}

#[derive(Debug, FromRow, Serialize)]
pub struct Post {
    pub id: i64,
    pub title: String,
    pub content: String,
    pub author_id: i64,
    pub published: bool,
    pub created_at: DateTime<Utc>,
}
```

### Pattern 3: Repository Pattern

```rust
// ✅ CORRECT: Repository with SQLx
use sqlx::PgPool;

pub struct UserRepository {
    pool: PgPool,
}

impl UserRepository {
    pub fn new(pool: PgPool) -> Self {
        Self { pool }
    }

    pub async fn find_by_id(&self, id: i64) -> Result<Option<User>, sqlx::Error> {
        sqlx::query_as!(
            User,
            r#"
            SELECT id, email, name, password_hash, is_active, created_at, updated_at
            FROM users
            WHERE id = $1
            "#,
            id
        )
        .fetch_optional(&self.pool)
        .await
    }

    pub async fn find_by_email(&self, email: &str) -> Result<Option<User>, sqlx::Error> {
        sqlx::query_as!(
            User,
            r#"SELECT * FROM users WHERE email = $1"#,
            email
        )
        .fetch_optional(&self.pool)
        .await
    }

    pub async fn create(
        &self,
        email: &str,
        name: &str,
        password_hash: &str,
    ) -> Result<User, sqlx::Error> {
        sqlx::query_as!(
            User,
            r#"
            INSERT INTO users (email, name, password_hash)
            VALUES ($1, $2, $3)
            RETURNING *
            "#,
            email,
            name,
            password_hash
        )
        .fetch_one(&self.pool)
        .await
    }

    pub async fn update(&self, id: i64, name: &str) -> Result<Option<User>, sqlx::Error> {
        sqlx::query_as!(
            User,
            r#"
            UPDATE users
            SET name = $2, updated_at = NOW()
            WHERE id = $1
            RETURNING *
            "#,
            id,
            name
        )
        .fetch_optional(&self.pool)
        .await
    }

    pub async fn delete(&self, id: i64) -> Result<bool, sqlx::Error> {
        let result = sqlx::query!(r#"DELETE FROM users WHERE id = $1"#, id)
            .execute(&self.pool)
            .await?;
        Ok(result.rows_affected() > 0)
    }

    pub async fn exists_by_email(&self, email: &str) -> Result<bool, sqlx::Error> {
        let result = sqlx::query!(
            r#"SELECT EXISTS(SELECT 1 FROM users WHERE email = $1) as "exists!""#,
            email
        )
        .fetch_one(&self.pool)
        .await?;
        Ok(result.exists)
    }
}
```

### Pattern 4: Transactions

```rust
// ✅ CORRECT: Transaction handling
use sqlx::{PgPool, Postgres, Transaction};

pub async fn transfer_funds(
    pool: &PgPool,
    from_id: i64,
    to_id: i64,
    amount: i64,
) -> Result<(), AppError> {
    let mut tx: Transaction<'_, Postgres> = pool.begin().await?;

    // Debit from source
    let from_balance = sqlx::query_scalar!(
        r#"
        UPDATE accounts
        SET balance = balance - $2
        WHERE id = $1 AND balance >= $2
        RETURNING balance
        "#,
        from_id,
        amount
    )
    .fetch_optional(&mut *tx)
    .await?
    .ok_or(AppError::InsufficientFunds)?;

    // Credit to destination
    sqlx::query!(
        r#"UPDATE accounts SET balance = balance + $2 WHERE id = $1"#,
        to_id,
        amount
    )
    .execute(&mut *tx)
    .await?;

    // Record transaction
    sqlx::query!(
        r#"
        INSERT INTO transactions (from_id, to_id, amount)
        VALUES ($1, $2, $3)
        "#,
        from_id,
        to_id,
        amount
    )
    .execute(&mut *tx)
    .await?;

    tx.commit().await?;
    Ok(())
}
```

### Pattern 5: Complex Queries

```rust
// ✅ CORRECT: Joins and aggregations
pub async fn get_posts_with_authors(
    pool: &PgPool,
    limit: i64,
) -> Result<Vec<PostWithAuthor>, sqlx::Error> {
    sqlx::query_as!(
        PostWithAuthor,
        r#"
        SELECT
            p.id,
            p.title,
            p.content,
            p.created_at,
            u.id as author_id,
            u.name as author_name
        FROM posts p
        JOIN users u ON p.author_id = u.id
        WHERE p.published = true
        ORDER BY p.created_at DESC
        LIMIT $1
        "#,
        limit
    )
    .fetch_all(pool)
    .await
}

pub async fn get_user_stats(pool: &PgPool, user_id: i64) -> Result<UserStats, sqlx::Error> {
    sqlx::query_as!(
        UserStats,
        r#"
        SELECT
            COUNT(p.id) as "post_count!",
            COALESCE(SUM(p.views), 0) as "total_views!",
            MAX(p.created_at) as last_post_at
        FROM posts p
        WHERE p.author_id = $1
        "#,
        user_id
    )
    .fetch_one(pool)
    .await
}
```

### Pattern 6: Migrations

```sql
-- migrations/001_create_users.sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    name VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_active BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);

-- migrations/002_create_posts.sql
CREATE TABLE posts (
    id BIGSERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    content TEXT NOT NULL,
    author_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    published BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_posts_author ON posts(author_id);
```

```bash
# SQLx CLI commands
sqlx database create
sqlx migrate add create_users
sqlx migrate run
sqlx migrate revert
```

## Anti-Patterns

### Don't: N+1 Queries

```rust
// ❌ BAD: N+1 query problem
let users = sqlx::query_as!(User, "SELECT * FROM users")
    .fetch_all(&pool).await?;
for user in &users {
    let posts = sqlx::query_as!(Post, "SELECT * FROM posts WHERE author_id = $1", user.id)
        .fetch_all(&pool).await?;  // Query per user!
}

// ✅ GOOD: Single query with join
let users_with_posts = sqlx::query_as!(
    UserWithPosts,
    r#"
    SELECT u.*, array_agg(p.*) as posts
    FROM users u
    LEFT JOIN posts p ON p.author_id = u.id
    GROUP BY u.id
    "#
)
.fetch_all(&pool).await?;
```

### Don't: String Interpolation

```rust
// ❌ BAD: SQL injection risk
let query = format!("SELECT * FROM users WHERE name = '{}'", name);

// ✅ GOOD: Parameterized queries
sqlx::query_as!(User, "SELECT * FROM users WHERE name = $1", name)
```

### Don't: Ignore Errors

```rust
// ❌ BAD: Ignoring database errors
let user = sqlx::query_as!(User, "SELECT * FROM users WHERE id = $1", id)
    .fetch_one(&pool)
    .await
    .unwrap();  // Panic on error!

// ✅ GOOD: Proper error handling
let user = sqlx::query_as!(User, "SELECT * FROM users WHERE id = $1", id)
    .fetch_optional(&pool)
    .await?
    .ok_or(AppError::NotFound)?;
```

## Resources

| Topic | Link |
|-------|------|
| SQLx Queries | [mdc:resources/queries.md] |
| Transactions | [mdc:resources/transactions.md] |
| Migrations | [mdc:resources/migrations.md] |
| Performance | [mdc:resources/performance.md] |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
