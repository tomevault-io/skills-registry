---
name: sqlserver-design
description: name: sqlserver-design Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: sqlserver-design
description: SQL Server schema design and query optimization
---

# SQL Server Design

> Efficient schema design and query optimization.

---

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Table | PascalCase, plural | Users, Orders |
| Column | PascalCase | FirstName |
| PK | Id | Id |
| FK | {Table}Id | UserId |
| Index | IX_{Table}_{Column} | IX_Users_Email |

---

## Standard Columns

```sql
CREATE TABLE Users (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    -- Business columns
    Email NVARCHAR(255) NOT NULL,
    Name NVARCHAR(100) NOT NULL,
    -- Audit columns
    CreatedAt DATETIME2 DEFAULT GETUTCDATE(),
    UpdatedAt DATETIME2 NULL,
    IsDeleted BIT DEFAULT 0
);
```

---

## Relationships

```sql
-- One-to-Many
ALTER TABLE Orders
ADD CONSTRAINT FK_Orders_Users
FOREIGN KEY (UserId) REFERENCES Users(Id);

-- Many-to-Many
CREATE TABLE UserRoles (
    UserId INT,
    RoleId INT,
    PRIMARY KEY (UserId, RoleId)
);
```

---

## Indexing

| Scenario | Index Type |
|----------|------------|
| Primary key | Clustered (auto) |
| Foreign key | Non-clustered |
| Search column | Non-clustered |
| Composite search | Composite |

```sql
CREATE INDEX IX_Users_Email ON Users(Email);
CREATE INDEX IX_Orders_UserId ON Orders(UserId);
```

---

## Query Patterns

```sql
-- Parameterized (safe)
SELECT * FROM Users WHERE Email = @Email

-- Pagination
SELECT * FROM Users
ORDER BY CreatedAt DESC
OFFSET @Skip ROWS
FETCH NEXT @Take ROWS ONLY
```

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Parameterized queries | String concatenation |
| Index foreign keys | Skip FK indexes |
| UTC timestamps | Local time |
| Soft delete | Hard delete (usually) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
