---
name: gin-go
description: Review Go code using Gin framework for routing patterns, middleware usage, request/response handling, and error conventions. Use when this capability is needed.
metadata:
  author: jcleira
---

# Gin Framework Review

Review Go code using the Gin web framework for best practices in routing, middleware, request/response handling, and error management.

## 1. Router & Route Groups

Review router configuration for explicit control and consistent patterns.

### Review Checklist

- **Issue** if using `gin.Default()` without explicit middleware control
- **Issue** if related routes aren't grouped with `router.Group()`
- **Issue** if paths use camelCase or snake_case (should use kebab-case)
- **Issue** if resource paths are singular (should be plural)
- **Issue** if incorrect HTTP verbs for CRUD operations

```go
// Issue: implicit middleware, no explicit control
router := gin.Default()

// Correct: explicit middleware configuration
router := gin.New()
router.Use(gin.Recovery())
router.Use(loggingMiddleware)
```

```go
// Issue: ungrouped related routes
router.GET("/users/:id", h.GetUser)
router.POST("/users", h.CreateUser)
router.GET("/users/:id/orders", h.GetUserOrders)

// Correct: grouped routes
users := router.Group("/users")
{
    users.GET("/:id", h.GetUser)
    users.POST("", h.CreateUser)
    users.GET("/:id/orders", h.GetUserOrders)
}
```

```go
// Issue: wrong path naming
router.GET("/getUserById/:id", h.GetUser)     // camelCase
router.GET("/user_orders", h.GetOrders)       // snake_case
router.GET("/user/:id", h.GetUser)            // singular

// Correct: kebab-case, plural resources
router.GET("/users/:id", h.GetUser)
router.GET("/user-orders", h.GetOrders)
```

---

## 2. Handler Structure

Handlers should be thin and delegate business logic to services.

### Review Checklist

- **Issue** if handlers contain business logic beyond HTTP concerns
- **Issue** if handler structs use value receivers
- **Issue** if errors aren't handled with early returns
- **Issue** if parameter extraction is inconsistent

```go
// Issue: business logic in handler
func (h UserHandler) Create(c *gin.Context) {  // value receiver
    var req CreateRequest
    c.BindJSON(&req)

    // Business logic - should be in service
    if req.Email != "" {
        // validate email format...
    }
    user := &User{Name: req.Name}
    h.db.Create(user)

    c.JSON(200, user)
}

// Correct: thin handler, pointer receiver
func (h *UserHandler) Create(c *gin.Context) {
    var req CreateRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    user, err := h.userService.Create(c.Request.Context(), &req)
    if err != nil {
        c.JSON(http.StatusInternalServerError, gin.H{"error": "failed to create user"})
        return
    }

    c.JSON(http.StatusCreated, user)
}
```

---

## 3. Middleware Patterns

Middleware should follow standard patterns for cross-cutting concerns.

### Review Checklist

- **Issue** if `c.Next()` is missing or misplaced in middleware
- **Issue** if no recovery middleware for panic handling
- **Issue** if logging middleware lacks request context (method, path, latency)
- **Issue** if auth middleware doesn't abort on failure

```go
// Issue: missing c.Next(), won't execute subsequent handlers
func LoggingMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        log.Println("request received")
        // Missing c.Next()!
    }
}

// Correct: structured logging middleware
func LoggingMiddleware(logger *slog.Logger) gin.HandlerFunc {
    return func(c *gin.Context) {
        start := time.Now()
        path := c.Request.URL.Path

        c.Next()

        logger.Info("request",
            "method", c.Request.Method,
            "path", path,
            "status", c.Writer.Status(),
            "latency", time.Since(start),
        )
    }
}
```

```go
// Issue: auth middleware doesn't abort
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")
        if token == "" {
            c.JSON(401, gin.H{"error": "unauthorized"})
            // Missing return! Continues to handler
        }
        c.Next()
    }
}

// Correct: abort on auth failure
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")
        if token == "" {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "unauthorized"})
            return
        }
        // Validate token...
        c.Set("userID", userID)
        c.Next()
    }
}
```

---

## 4. Request Binding & Validation

Use proper binding methods and struct tag validation.

### Review Checklist

- **Issue** if using `BindJSON` instead of `ShouldBindJSON`
- **Issue** if binding errors aren't handled explicitly
- **Issue** if required fields lack `binding:"required"` tag
- **Issue** if validation errors expose internal details

```go
// Issue: BindJSON aborts on error, loses control
func (h *Handler) Create(c *gin.Context) {
    var req Request
    c.BindJSON(&req)  // Aborts with 400 on error
    // Code below may not execute
}

// Correct: ShouldBindJSON for explicit error handling
func (h *Handler) Create(c *gin.Context) {
    var req Request
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{
            "error": "invalid request body",
            "details": formatValidationErrors(err),
        })
        return
    }
}
```

```go
// Issue: missing validation tags
type CreateUserRequest struct {
    Email string `json:"email"`
    Name  string `json:"name"`
}

// Correct: validation with struct tags
type CreateUserRequest struct {
    Email string `json:"email" binding:"required,email"`
    Name  string `json:"name" binding:"required,min=1,max=100"`
    Age   int    `json:"age" binding:"omitempty,gte=0,lte=150"`
}
```

---

## 5. Response Conventions

Maintain consistent response structure and status codes.

### Review Checklist

- **Issue** if JSON responses lack consistent structure
- **Issue** if wrong HTTP status codes for operations
- **Issue** if `c.JSON` called after `c.Abort*` methods
- **Issue** if internal errors exposed to clients

```go
// Issue: inconsistent response structures
c.JSON(200, user)                           // Direct object
c.JSON(200, gin.H{"user": user})            // Wrapped
c.JSON(200, gin.H{"data": user, "ok": true})// Different wrapper

// Correct: consistent response structure
type Response struct {
    Data  interface{} `json:"data,omitempty"`
    Error *ErrorInfo  `json:"error,omitempty"`
}

c.JSON(http.StatusOK, Response{Data: user})
```

```go
// Issue: c.JSON after abort
func (h *Handler) Get(c *gin.Context) {
    if !authorized {
        c.AbortWithStatus(http.StatusUnauthorized)
        c.JSON(http.StatusUnauthorized, gin.H{"error": "unauthorized"}) // Wrong!
        return
    }
}

// Correct: use AbortWithStatusJSON
func (h *Handler) Get(c *gin.Context) {
    if !authorized {
        c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "unauthorized"})
        return
    }
}
```

### HTTP Status Code Reference

| Operation | Success | Client Error | Not Found |
|-----------|---------|--------------|-----------|
| GET | 200 OK | 400 Bad Request | 404 Not Found |
| POST (create) | 201 Created | 400/422 | - |
| PUT/PATCH | 200 OK | 400/422 | 404 Not Found |
| DELETE | 204 No Content | 400 | 404 Not Found |

---

## 6. Context Usage

Handle Gin context correctly to avoid memory leaks and race conditions.

### Review Checklist

- **Issue** if `*gin.Context` is stored in structs or passed to goroutines
- **Issue** if `c` is passed to service layer instead of `c.Request.Context()`
- **Issue** if context values not retrieved safely with type assertion
- **Issue** if context cancellation not handled in long operations

```go
// Issue: storing gin.Context
type Handler struct {
    ctx *gin.Context  // Never do this!
}

// Issue: passing gin.Context to service
func (h *Handler) Get(c *gin.Context) {
    h.service.Process(c)  // Wrong! Pass standard context
}

// Correct: pass request context
func (h *Handler) Get(c *gin.Context) {
    result, err := h.service.Process(c.Request.Context())
}
```

```go
// Issue: unsafe context value retrieval
func (h *Handler) Get(c *gin.Context) {
    userID := c.Get("userID").(string)  // Panics if not set or wrong type
}

// Correct: safe retrieval with MustGet or type check
func (h *Handler) Get(c *gin.Context) {
    userID, exists := c.Get("userID")
    if !exists {
        c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "unauthorized"})
        return
    }
    uid, ok := userID.(string)
    if !ok {
        c.AbortWithStatusJSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
}
```

```go
// Issue: gin.Context in goroutine
func (h *Handler) Process(c *gin.Context) {
    go func() {
        // c may be reused by this point!
        data := c.Query("data")
    }()
}

// Correct: copy needed values before goroutine
func (h *Handler) Process(c *gin.Context) {
    data := c.Query("data")
    ctx := c.Request.Context()
    go func() {
        // Use copied values
        processAsync(ctx, data)
    }()
}
```

---

## 7. Error Handling

Centralize error handling and avoid leaking internal details.

### Review Checklist

- **Issue** if no centralized error handling middleware
- **Issue** if internal error messages exposed to clients
- **Issue** if errors not logged with request context
- **Issue** if custom error types don't map to HTTP status codes

```go
// Issue: exposing internal errors
func (h *Handler) Get(c *gin.Context) {
    user, err := h.service.GetUser(c.Request.Context(), id)
    if err != nil {
        c.JSON(500, gin.H{"error": err.Error()})  // Leaks internal details!
        return
    }
}

// Correct: generic message, log internal error
func (h *Handler) Get(c *gin.Context) {
    user, err := h.service.GetUser(c.Request.Context(), id)
    if err != nil {
        h.logger.ErrorContext(c.Request.Context(), "failed to get user",
            "error", err,
            "user_id", id,
        )
        c.JSON(http.StatusInternalServerError, gin.H{"error": "failed to retrieve user"})
        return
    }
}
```

```go
// Correct: centralized error middleware with custom errors
type AppError struct {
    Code    string
    Message string
    Status  int
    Err     error
}

func ErrorMiddleware(logger *slog.Logger) gin.HandlerFunc {
    return func(c *gin.Context) {
        c.Next()

        if len(c.Errors) > 0 {
            err := c.Errors.Last().Err
            if appErr, ok := err.(*AppError); ok {
                logger.ErrorContext(c.Request.Context(), appErr.Message,
                    "error", appErr.Err,
                    "code", appErr.Code,
                )
                c.JSON(appErr.Status, gin.H{
                    "error": gin.H{
                        "code":    appErr.Code,
                        "message": appErr.Message,
                    },
                })
                return
            }
            // Unknown error
            logger.ErrorContext(c.Request.Context(), "unhandled error", "error", err)
            c.JSON(http.StatusInternalServerError, gin.H{"error": "internal server error"})
        }
    }
}
```

---

## Output Format

When reviewing Gin code, report findings as:

```
**Gin Framework Review**

Issues found:
- `handlers/user.go:15` - Using `gin.Default()`, prefer `gin.New()` with explicit middleware
- `handlers/user.go:42` - `BindJSON` should be `ShouldBindJSON` for explicit error handling
- `handlers/user.go:58` - `*gin.Context` passed to service, use `c.Request.Context()`
- `middleware/auth.go:23` - Missing `return` after `c.AbortWithStatusJSON`

Recommendations:
- Add centralized error handling middleware
- Group related routes under `/api/v1`
- Add `binding:"required"` tags to request struct fields

No issues:
- Recovery middleware configured
- Consistent JSON response structure
- Proper use of HTTP status codes
```

---

## When to Run

This skill auto-invokes when reviewing Go files that:
- Import `github.com/gin-gonic/gin`
- Contain Gin handler functions (`*gin.Context` parameter)
- Define middleware (`gin.HandlerFunc` return type)
- Configure router setup (`gin.New()`, `gin.Default()`, `router.Group()`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcleira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
