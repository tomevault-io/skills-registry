---
name: go-repository
description: Repository pattern review for Go codebases using Jet ORM, domain models, and adapter patterns. Use when this capability is needed.
metadata:
  author: jcleira
---

# Go Repository Pattern Review

Review Go repositories for correct implementation of the repository pattern with Jet ORM, domain models, and adapter patterns.

## 1. Repository Structure

Repositories should follow a consistent file organization pattern.

### Expected Structure

```
repositories/<resource>/
├── repository.go        # Repository struct, constructor, interfaces
├── adapters.go          # Domain ↔ Database model conversion
├── create.go            # Create operations
├── read.go              # Read operations (Get, List, etc.)
├── update.go            # Update operations
├── delete.go            # Delete operations
└── repository_test.go   # Test file with parallel tests
```

### Review Checklist

- **Issue** if all CRUD methods are in a single file (should be separated)
- **Issue** if adapters are in the same file as repository methods
- **OK** for small repositories (1-2 methods) to use fewer files

---

## 2. Constructor Pattern

Repositories must use the standard constructor with dependency injection.

### Review Checklist

- **Issue** if constructor is not named `New` or `NewRepository`
- **Issue** if constructor doesn't accept `dbGetter` parameter
- **Issue** if constructor doesn't accept `transactor` parameter
- **Issue** if `dbGetter` and `transactor` are not stored in struct fields

```go
// Issue: missing dependencies
func NewRepository() *Repository {
    return &Repository{}
}

// Correct: standard constructor pattern
type Repository struct {
    dbGetter   dbGetter
    transactor transactor
}

func New(dbGetter dbGetter, transactor transactor) *Repository {
    return &Repository{
        dbGetter:   dbGetter,
        transactor: transactor,
    }
}
```

### Dependency Types

```go
// Standard dependency interfaces
type dbGetter interface {
    GetDB(ctx context.Context) DBInterface
}

type transactor interface {
    WithinTransaction(ctx context.Context, fn func(context.Context) error) error
}
```

---

## 3. Method Signatures

Repository methods must follow consistent signature patterns.

### Review Checklist

- **Issue** if `context.Context` is not the first parameter
- **Issue** if method names are not descriptive (e.g., `Get` instead of `GetByID`)
- **Issue** if return types are database models instead of domain models
- **Issue** if methods return `(T, error)` for single items (should return `(*T, error)`)

```go
// Issue: no context, bad naming, database model return
func (r *Repository) Get(id string) (model.User, error) { ... }

// Correct: context first, clear name, domain model return
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) { ... }
```

### Common Method Patterns

| Method | Signature |
|--------|-----------|
| Get by ID | `GetByID(ctx context.Context, id string) (*domain.T, error)` |
| List | `List(ctx context.Context, filter Filter) ([]*domain.T, error)` |
| Create | `Create(ctx context.Context, entity *domain.T) (*domain.T, error)` |
| Update | `Update(ctx context.Context, entity *domain.T) (*domain.T, error)` |
| Delete | `Delete(ctx context.Context, id string) error` |

---

## 4. CRUD Operations

### Create Pattern

```go
// Correct: Create with adapter usage
func (r *Repository) Create(ctx context.Context, user *domain.User) (*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)

    dbUser := toDBUser(user)  // Use adapter

    stmt := table.Users.INSERT(
        table.Users.AllColumns,
    ).MODEL(dbUser).RETURNING(table.Users.AllColumns)

    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        return nil, fmt.Errorf("failed to create user: %w", err)
    }

    return toDomainUser(&result), nil  // Use adapter
}
```

### Review Checklist

- **Issue** if Create doesn't use RETURNING clause
- **Issue** if Create doesn't convert domain → database model before insert
- **Issue** if Create doesn't convert database → domain model before return
- **Issue** if Create modifies the input parameter

---

## 5. Read Operations

### GetByID Pattern

```go
// Correct: GetByID with qrm.ErrNoRows handling
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)

    stmt := table.Users.SELECT(table.Users.AllColumns).
        WHERE(table.Users.ID.EQ(postgres.String(id)))

    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        if errors.Is(err, qrm.ErrNoRows) {
            return nil, domain.ErrUserNotFound
        }
        return nil, fmt.Errorf("failed to get user %s: %w", id, err)
    }

    return toDomainUser(&result), nil
}
```

### List Pattern

```go
// Correct: List with filter and pagination
func (r *Repository) List(ctx context.Context, filter Filter) ([]*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)

    stmt := table.Users.SELECT(table.Users.AllColumns)

    // Apply filters
    if filter.Status != "" {
        stmt = stmt.WHERE(table.Users.Status.EQ(postgres.String(filter.Status)))
    }

    // Apply pagination
    stmt = stmt.ORDER_BY(table.Users.CreatedAt.DESC()).
        LIMIT(filter.Limit).
        OFFSET(filter.Offset)

    var results []model.User
    if err := stmt.Query(db, &results); err != nil {
        return nil, fmt.Errorf("failed to list users: %w", err)
    }

    return toDomainUsers(results), nil
}
```

### Review Checklist

- **Issue** if GetByID doesn't check for `qrm.ErrNoRows`
- **Issue** if qrm.ErrNoRows returns generic error instead of domain error
- **Issue** if List doesn't handle empty results (should return empty slice, not nil)
- **Issue** if List doesn't support pagination parameters

---

## 6. Update Operations

```go
// Correct: Update with selective field updates
func (r *Repository) Update(ctx context.Context, user *domain.User) (*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)

    dbUser := toDBUser(user)

    stmt := table.Users.UPDATE(
        table.Users.Name,
        table.Users.Email,
        table.Users.UpdatedAt,
    ).MODEL(dbUser).
        WHERE(table.Users.ID.EQ(postgres.String(user.ID))).
        RETURNING(table.Users.AllColumns)

    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        if errors.Is(err, qrm.ErrNoRows) {
            return nil, domain.ErrUserNotFound
        }
        return nil, fmt.Errorf("failed to update user %s: %w", user.ID, err)
    }

    return toDomainUser(&result), nil
}
```

### Review Checklist

- **Issue** if Update uses `AllColumns` (should explicitly list updateable fields)
- **Issue** if Update doesn't check for `qrm.ErrNoRows`
- **Issue** if Update modifies immutable fields (ID, CreatedAt)
- **Issue** if Update doesn't use RETURNING clause

---

## 7. Delete Operations

```go
// Correct: Delete with existence check
func (r *Repository) Delete(ctx context.Context, id string) error {
    db := r.dbGetter.GetDB(ctx)

    stmt := table.Users.DELETE().
        WHERE(table.Users.ID.EQ(postgres.String(id)))

    result, err := stmt.Exec(db)
    if err != nil {
        return fmt.Errorf("failed to delete user %s: %w", id, err)
    }

    rowsAffected, err := result.RowsAffected()
    if err != nil {
        return fmt.Errorf("failed to get rows affected: %w", err)
    }

    if rowsAffected == 0 {
        return domain.ErrUserNotFound
    }

    return nil
}
```

### Review Checklist

- **Issue** if Delete doesn't check RowsAffected
- **Issue** if Delete doesn't return domain error when entity not found
- **Issue** if soft delete uses DELETE instead of UPDATE

---

## 8. Error Handling

### Review Checklist

- **Issue** if `qrm.ErrNoRows` is not checked in read operations
- **Issue** if `qrm.ErrNoRows` returns database error instead of domain error
- **Issue** if errors are not wrapped with context
- **Issue** if errors don't include relevant IDs or parameters

```go
// Issue: no qrm.ErrNoRows check
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        return nil, err  // Returns qrm.ErrNoRows directly!
    }
    return toDomainUser(&result), nil
}

// Correct: convert to domain error
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        if errors.Is(err, qrm.ErrNoRows) {
            return nil, domain.ErrUserNotFound
        }
        return nil, fmt.Errorf("failed to get user %s: %w", id, err)
    }
    return toDomainUser(&result), nil
}
```

### Domain Error Pattern

```go
// Domain errors in domain package
var (
    ErrUserNotFound      = errors.New("user not found")
    ErrUserAlreadyExists = errors.New("user already exists")
    ErrInvalidUser       = errors.New("invalid user")
)
```

---

## 9. Adapters

Adapters convert between domain models and database models.

### Review Checklist

- **Issue** if adapters are not in separate file (`adapters.go`)
- **Issue** if adapter names don't follow `toDomain*` and `toDB*` pattern
- **Issue** if adapters are public (should be private)
- **Issue** if repository methods return database models directly

```go
// Correct: adapter pattern in adapters.go
func toDomainUser(dbUser *model.User) *domain.User {
    if dbUser == nil {
        return nil
    }
    return &domain.User{
        ID:        dbUser.ID,
        Email:     dbUser.Email,
        Name:      dbUser.Name,
        Status:    domain.UserStatus(dbUser.Status),
        CreatedAt: dbUser.CreatedAt,
        UpdatedAt: dbUser.UpdatedAt,
    }
}

func toDBUser(user *domain.User) *model.User {
    if user == nil {
        return nil
    }
    return &model.User{
        ID:        user.ID,
        Email:     user.Email,
        Name:      user.Name,
        Status:    string(user.Status),
        CreatedAt: user.CreatedAt,
        UpdatedAt: user.UpdatedAt,
    }
}

// Slice adapters
func toDomainUsers(dbUsers []model.User) []*domain.User {
    users := make([]*domain.User, len(dbUsers))
    for i, dbUser := range dbUsers {
        users[i] = toDomainUser(&dbUser)
    }
    return users
}
```

### Adapter Naming Convention

| Conversion | Function Name |
|------------|---------------|
| DB → Domain | `toDomainUser(*model.User) *domain.User` |
| Domain → DB | `toDBUser(*domain.User) *model.User` |
| DB slice → Domain | `toDomainUsers([]model.User) []*domain.User` |

---

## 10. Transaction Patterns

Use `transactor.WithinTransaction` for multi-step operations.

### Review Checklist

- **Issue** if multi-step operations don't use transactions
- **Issue** if transactions are manually managed (BEGIN/COMMIT/ROLLBACK)
- **Issue** if `dbGetter.GetDB(ctx)` is called outside transaction function

```go
// Correct: transaction pattern for multi-step operation
func (r *Repository) CreateUserWithProfile(ctx context.Context, user *domain.User, profile *domain.Profile) error {
    return r.transactor.WithinTransaction(ctx, func(txCtx context.Context) error {
        db := r.dbGetter.GetDB(txCtx)  // Use txCtx, not ctx!

        // Step 1: Insert user
        dbUser := toDBUser(user)
        stmt := table.Users.INSERT(table.Users.AllColumns).
            MODEL(dbUser).
            RETURNING(table.Users.AllColumns)

        var resultUser model.User
        if err := stmt.Query(db, &resultUser); err != nil {
            return fmt.Errorf("failed to create user: %w", err)
        }

        // Step 2: Insert profile
        dbProfile := toDBProfile(profile)
        dbProfile.UserID = resultUser.ID

        stmt2 := table.Profiles.INSERT(table.Profiles.AllColumns).
            MODEL(dbProfile)

        if _, err := stmt2.Exec(db); err != nil {
            return fmt.Errorf("failed to create profile: %w", err)
        }

        return nil  // Commit on success
    })
}
```

### When to Use Transactions

- Creating related entities
- Update operations affecting multiple tables
- Delete with cascade cleanup
- Any operation requiring atomicity

---

## 11. Database Access

### Review Checklist

- **Issue** if database is stored in repository struct
- **Issue** if `dbGetter.GetDB()` is called without context
- **Issue** if SQL is used instead of Jet ORM queries
- **OK** for complex queries to use raw SQL with `db.QueryContext()`

```go
// Issue: database stored in struct
type Repository struct {
    db *sql.DB  // Wrong! Use dbGetter
}

// Issue: SQL instead of Jet
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)
    row := db.QueryRow("SELECT * FROM users WHERE id = $1", id)  // Use Jet!
}

// Correct: Jet ORM usage
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    db := r.dbGetter.GetDB(ctx)
    stmt := table.Users.SELECT(table.Users.AllColumns).
        WHERE(table.Users.ID.EQ(postgres.String(id)))

    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        if errors.Is(err, qrm.ErrNoRows) {
            return nil, domain.ErrUserNotFound
        }
        return nil, fmt.Errorf("failed to get user: %w", err)
    }
    return toDomainUser(&result), nil
}
```

### When Raw SQL is Acceptable

- Complex joins that are verbose in Jet
- Database-specific features (window functions, CTEs)
- Performance-critical queries requiring optimization
- Always use `db.QueryContext(ctx, ...)` with context

---

## 12. Testing

Repository tests should be thorough and follow Go testing conventions.

### Review Checklist

- **Issue** if repository has no test file
- **Issue** if tests don't use `t.Parallel()`
- **Issue** if tests don't use table-driven test pattern
- **Issue** if tests don't verify error cases
- **Issue** if tests use real database instead of test container

```go
// Correct: table-driven parallel tests
func TestRepository_GetByID(t *testing.T) {
    t.Parallel()

    tests := []struct {
        name    string
        id      string
        want    *domain.User
        wantErr error
    }{
        {
            name: "existing user",
            id:   "user-123",
            want: &domain.User{ID: "user-123", Name: "John"},
            wantErr: nil,
        },
        {
            name: "non-existent user",
            id:   "user-999",
            want: nil,
            wantErr: domain.ErrUserNotFound,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            t.Parallel()

            // Setup
            repo := setupTestRepo(t)
            if tt.want != nil {
                seedUser(t, repo, tt.want)
            }

            // Execute
            got, err := repo.GetByID(context.Background(), tt.id)

            // Assert
            if !errors.Is(err, tt.wantErr) {
                t.Errorf("GetByID() error = %v, wantErr %v", err, tt.wantErr)
            }
            if !reflect.DeepEqual(got, tt.want) {
                t.Errorf("GetByID() = %v, want %v", got, tt.want)
            }
        })
    }
}
```

---

## 13. Domain Model Integration

### Review Checklist

- **Issue** if repository methods return database models (`model.*`)
- **Issue** if repository methods accept database models as parameters
- **Issue** if domain models are imported in database model files
- **OK** for adapters to reference both domain and database models

```go
// Issue: leaking database model
func (r *Repository) GetByID(ctx context.Context, id string) (*model.User, error) {
    // Returns database model!
}

// Correct: return domain model
func (r *Repository) GetByID(ctx context.Context, id string) (*domain.User, error) {
    var result model.User
    if err := stmt.Query(db, &result); err != nil {
        // handle error
    }
    return toDomainUser(&result), nil  // Convert before return
}
```

---

## 14. Common Anti-Patterns

### Anti-Pattern: God Repository

```go
// Issue: repository doing too much
func (r *Repository) GetUserWithOrdersAndPayments(ctx context.Context, id string) (*ComplexDTO, error) {
    // Multiple joins, complex business logic
}
```

**Solution**: Keep repositories focused on single aggregate root.

### Anti-Pattern: Business Logic in Repository

```go
// Issue: validation in repository
func (r *Repository) Create(ctx context.Context, user *domain.User) error {
    if user.Email == "" {
        return errors.New("email required")  // Wrong layer!
    }
}
```

**Solution**: Validate in service layer, not repository.

### Anti-Pattern: Ignoring qrm.ErrNoRows

```go
// Issue: not checking ErrNoRows
if err := stmt.Query(db, &result); err != nil {
    return nil, err  // Exposes database error!
}
```

**Solution**: Always check `qrm.ErrNoRows` and convert to domain error.

### Anti-Pattern: Storing DB Connection

```go
// Issue: storing connection
type Repository struct {
    db *sql.DB
}
```

**Solution**: Use `dbGetter.GetDB(ctx)` to get connection per operation.

---

## Output Format

When reviewing repository code, report findings as:

```
**Repository Pattern Review**

Issues found:
- `repositories/user/repository.go:15` - Constructor missing `transactor` parameter
- `repositories/user/create.go:42` - Returns `*model.User` instead of `*domain.User`
- `repositories/user/read.go:58` - GetByID doesn't check for `qrm.ErrNoRows`
- `repositories/user/adapters.go:12` - Adapter `ToDomainUser` is public (should be private)

Recommendations:
- Add `repository_test.go` with table-driven tests
- Separate CRUD methods into individual files
- Add domain error definitions for NotFound cases

No issues:
- Constructor follows standard pattern
- Adapters properly convert domain ↔ database models
- Transaction pattern used correctly in CreateWithProfile
```

---

## When to Run

This skill auto-invokes when reviewing Go files that:
- Are in `repositories/` or `repository/` directories
- Define repository interfaces or structs
- Contain `dbGetter` or `transactor` fields
- Use Jet ORM imports (`github.com/go-jet/jet`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcleira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
