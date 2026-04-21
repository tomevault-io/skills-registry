---
name: backend-controller
description: Generate REST controllers for Spring Boot 3.4.x with OpenAPI documentation and exception handling. Use this when asked to create API endpoints, REST controllers, HTTP handlers, custom exceptions, or error handling. Use when this capability is needed.
metadata:
  author: sliard
---

# REST Controller Generation

Generate REST controllers following project conventions for Spring Boot 3.4.x.

## Controller Structure

```java
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
@Tag(name = "Products", description = "Product management")
public class ProductController {

    private final ProductService productService;

    @GetMapping
    @Operation(summary = "List all products")
    public ResponseEntity<Page<ProductResponse>> findAll(Pageable pageable) {
        return ResponseEntity.ok(productService.findAll(pageable));
    }

    @GetMapping("/{id}")
    @Operation(summary = "Get product by ID")
    public ResponseEntity<ProductResponse> findById(@PathVariable UUID id) {
        return ResponseEntity.ok(productService.findById(id));
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    @Operation(summary = "Create a product")
    public ProductResponse create(@Valid @RequestBody ProductRequest request) {
        return productService.create(request);
    }

    @PutMapping("/{id}")
    @Operation(summary = "Update a product")
    public ResponseEntity<ProductResponse> update(
            @PathVariable UUID id,
            @Valid @RequestBody ProductRequest request) {
        return ResponseEntity.ok(productService.update(id, request));
    }

    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    @Operation(summary = "Delete a product")
    public void delete(@PathVariable UUID id) {
        productService.delete(id);
    }
}
```

## URL Conventions

- Prefix: `/api/`
- Resource names: plural, lowercase (`/api/products`, `/api/users`)
- Nested resources: `/api/categories/{categoryId}/products`
- Actions: POST to `/api/products/{id}/publish`

## HTTP Methods and Status Codes

| Action | Method | URL | Success Status |
|--------|--------|-----|----------------|
| List | GET | /api/products | 200 OK |
| Get one | GET | /api/products/{id} | 200 OK |
| Create | POST | /api/products | 201 Created |
| Update | PUT | /api/products/{id} | 200 OK |
| Partial update | PATCH | /api/products/{id} | 200 OK |
| Delete | DELETE | /api/products/{id} | 204 No Content |

## Request Parameters

### Query Parameters
```java
@GetMapping
public Page<ProductResponse> findAll(
    @RequestParam(required = false) String name,
    @RequestParam(required = false) UUID categoryId,
    @RequestParam(required = false) BigDecimal minPrice,
    Pageable pageable
) { ... }
```

### Path Variables
```java
@GetMapping("/{id}")
public ProductResponse findById(@PathVariable UUID id) { ... }
```

### Request Body
```java
@PostMapping
public ProductResponse create(@Valid @RequestBody ProductRequest request) { ... }
```

## Pagination

Automatic via Spring Data:
```
GET /api/products?page=0&size=20&sort=name,asc
```

Response format includes `content`, `totalElements`, `totalPages`, etc.

## OpenAPI Documentation

```java
@Operation(
    summary = "Short description",
    description = "Detailed description"
)
@ApiResponse(responseCode = "200", description = "Success")
@ApiResponse(responseCode = "404", description = "Not found")
```

## Security with @PreAuthorize

```java
@GetMapping
@PreAuthorize("hasRole('USER')")
public List<ProductResponse> findAll() { ... }

@DeleteMapping("/{id}")
@PreAuthorize("hasRole('ADMIN')")
public void delete(@PathVariable UUID id) { ... }
```

## Access Current User

```java
@GetMapping("/me")
public UserResponse getCurrentUser(@AuthenticationPrincipal User user) {
    return userService.toResponse(user);
}
```

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(EntityNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("NOT_FOUND", ex.getMessage()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        var errors = ex.getBindingResult().getFieldErrors().stream()
            .map(e -> e.getField() + ": " + e.getDefaultMessage())
            .toList();
        return ResponseEntity.badRequest()
            .body(new ErrorResponse("VALIDATION_ERROR", String.join(", ", errors)));
    }
}
```

---

## Exception Handling

### Error Response Structure

```java
public record ErrorResponse(
    Instant timestamp,
    int status,
    String error,
    String code,
    String message,
    List<FieldError> details,
    String path
) {
    public record FieldError(String field, String message) {}

    public static ErrorResponse of(HttpStatus status, String code, String message, String path) {
        return new ErrorResponse(
            Instant.now(),
            status.value(),
            status.getReasonPhrase(),
            code,
            message,
            List.of(),
            path
        );
    }

    public static ErrorResponse withDetails(HttpStatus status, String code, String message, 
                                            List<FieldError> details, String path) {
        return new ErrorResponse(
            Instant.now(),
            status.value(),
            status.getReasonPhrase(),
            code,
            message,
            details,
            path
        );
    }
}
```

### Base Exception Classes

```java
public abstract class BusinessException extends RuntimeException {
    
    private final String code;
    private final HttpStatus status;

    protected BusinessException(String message, String code, HttpStatus status) {
        super(message);
        this.code = code;
        this.status = status;
    }

    public String getCode() { return code; }
    public HttpStatus getStatus() { return status; }
}

public class ResourceNotFoundException extends BusinessException {
    public ResourceNotFoundException(String resourceName, Object id) {
        super(String.format("%s not found with id: %s", resourceName, id),
              "RESOURCE_NOT_FOUND", HttpStatus.NOT_FOUND);
    }
}

public class ResourceAlreadyExistsException extends BusinessException {
    public ResourceAlreadyExistsException(String resourceName, String field, Object value) {
        super(String.format("%s already exists with %s: %s", resourceName, field, value),
              "RESOURCE_ALREADY_EXISTS", HttpStatus.CONFLICT);
    }
}

public class InvalidOperationException extends BusinessException {
    public InvalidOperationException(String message) {
        super(message, "INVALID_OPERATION", HttpStatus.UNPROCESSABLE_ENTITY);
    }
}
```

### Domain-Specific Exception Example

```java
public class OrderNotFoundException extends BusinessException {
    public OrderNotFoundException(UUID orderId) {
        super(String.format("Order not found: %s", orderId),
              "ORDER_NOT_FOUND", HttpStatus.NOT_FOUND);
    }
}
```

### Global Exception Handler

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(
            BusinessException ex, HttpServletRequest request) {
        log.warn("Business exception: {} - {}", ex.getCode(), ex.getMessage());
        return ResponseEntity.status(ex.getStatus())
            .body(ErrorResponse.of(ex.getStatus(), ex.getCode(), ex.getMessage(), request.getRequestURI()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(
            MethodArgumentNotValidException ex, HttpServletRequest request) {
        var fieldErrors = ex.getBindingResult().getFieldErrors().stream()
            .map(e -> new ErrorResponse.FieldError(e.getField(), e.getDefaultMessage()))
            .toList();
        return ResponseEntity.badRequest()
            .body(ErrorResponse.withDetails(HttpStatus.BAD_REQUEST, "VALIDATION_ERROR",
                "Validation failed", fieldErrors, request.getRequestURI()));
    }

    @ExceptionHandler(DataIntegrityViolationException.class)
    public ResponseEntity<ErrorResponse> handleDataIntegrity(
            DataIntegrityViolationException ex, HttpServletRequest request) {
        log.error("Data integrity violation", ex);
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(ErrorResponse.of(HttpStatus.CONFLICT, "DATA_INTEGRITY_ERROR",
                "Data integrity violation", request.getRequestURI()));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(
            Exception ex, HttpServletRequest request) {
        log.error("Unexpected error", ex);
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(ErrorResponse.of(HttpStatus.INTERNAL_SERVER_ERROR, "INTERNAL_ERROR",
                "An unexpected error occurred", request.getRequestURI()));
    }
}
```

### Exception Package Structure

```
src/main/java/com/example/app/
└── exception/
    ├── ErrorResponse.java
    ├── GlobalExceptionHandler.java
    ├── BusinessException.java
    ├── ResourceNotFoundException.java
    ├── ResourceAlreadyExistsException.java
    └── InvalidOperationException.java
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sliard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
