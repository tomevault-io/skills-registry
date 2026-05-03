---
name: springboot-patterns
description: Spring Boot architecture patterns, REST API design, layered services, data access, caching, async processing, and logging. Use for Java Spring Boot backend work. Use when this capability is needed.
metadata:
  author: skiluo
---

# Spring Boot Development Patterns

Spring Boot architecture and API patterns for scalable, production-grade services.

## When to Activate

- Building REST APIs with Spring MVC or WebFlux
- Structuring controller → service → repository layers
- Configuring Spring Data JPA, caching, or async processing
- Adding validation, exception handling, or pagination
- Setting up profiles for dev/staging/production environments
- Implementing event-driven patterns with Spring Events or Kafka

## REST API Structure

### Response Format Standard

All API responses follow a consistent envelope format:

**Success Response:**
```json
{
  "data": { /* actual response data */ },
  "errorCode": null,
  "errorMessage": null
}
```

**Error Response:**
```json
{
  "data": null,
  "errorCode": "validation_error",
  "errorMessage": "Request validation failed"
}
```

### Response Wrapper Classes

```java
public record ApiResponse<T>(
    T data,
    String errorCode,
    String errorMessage) {
  
  public static <T> ApiResponse<T> success(T data) {
    return new ApiResponse<>(data, null, null);
  }
  
  public static <T> ApiResponse<T> error(String errorCode, String errorMessage) {
    return new ApiResponse<>(null, errorCode, errorMessage);
  }
}
```

### Controller Implementation

```java
@RestController
@RequestMapping("/api/markets")
@Validated
class MarketController {
  private final MarketService marketService;

  MarketController(MarketService marketService) {
    this.marketService = marketService;
  }

  @GetMapping
  ResponseEntity<ApiResponse<Page<MarketResponse>>> list(
      @RequestParam(defaultValue = "0") int page,
      @RequestParam(defaultValue = "20") int size) {
    Page<Market> markets = marketService.list(PageRequest.of(page, size));
    Page<MarketResponse> response = markets.map(MarketResponse::from);
    return ResponseEntity.ok(ApiResponse.success(response));
  }

  @PostMapping
  ResponseEntity<ApiResponse<MarketResponse>> create(@Valid @RequestBody CreateMarketRequest request) {
    Market market = marketService.create(request);
    MarketResponse response = MarketResponse.from(market);
    return ResponseEntity.status(HttpStatus.CREATED)
        .body(ApiResponse.success(response));
  }
  
  @GetMapping("/{id}")
  ResponseEntity<ApiResponse<MarketResponse>> getById(@PathVariable Long id) {
    Market market = marketService.getById(id);
    return ResponseEntity.ok(ApiResponse.success(MarketResponse.from(market)));
  }
}
```

## Repository Pattern (Spring Data JPA)

```java
public interface MarketRepository extends JpaRepository<MarketEntity, Long> {
  @Query("select m from MarketEntity m where m.status = :status order by m.volume desc")
  List<MarketEntity> findActive(@Param("status") MarketStatus status, Pageable pageable);
}
```

## Service Layer with Transactions

```java
@Service
public class MarketService {
  private final MarketRepository repo;

  public MarketService(MarketRepository repo) {
    this.repo = repo;
  }

  @Transactional
  public Market create(CreateMarketRequest request) {
    MarketEntity entity = MarketEntity.from(request);
    MarketEntity saved = repo.save(entity);
    return Market.from(saved);
  }
}
```

## DTOs and Validation

```java
public record CreateMarketRequest(
    @NotBlank @Size(max = 200) String name,
    @NotBlank @Size(max = 2000) String description,
    @NotNull @FutureOrPresent Instant endDate,
    @NotEmpty List<@NotBlank String> categories) {}

public record MarketResponse(Long id, String name, MarketStatus status) {
  static MarketResponse from(Market market) {
    return new MarketResponse(market.id(), market.name(), market.status());
  }
}
```

## Exception Handling

Global exception handler that returns standardized `ApiResponse` format:

```java
@ControllerAdvice
class GlobalExceptionHandler {
  private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);
  
  @ExceptionHandler(MethodArgumentNotValidException.class)
  ResponseEntity<ApiResponse<Object>> handleValidation(MethodArgumentNotValidException ex) {
    String message = ex.getBindingResult().getFieldErrors().stream()
        .map(e -> e.getField() + ": " + e.getDefaultMessage())
        .collect(Collectors.joining(", "));
    return ResponseEntity.status(HttpStatus.BAD_REQUEST)
        .body(ApiResponse.error("validation_error", message));
  }

  @ExceptionHandler(EntityNotFoundException.class)
  ResponseEntity<ApiResponse<Object>> handleNotFound(EntityNotFoundException ex) {
    return ResponseEntity.status(HttpStatus.NOT_FOUND)
        .body(ApiResponse.error("not_found", ex.getMessage()));
  }

  @ExceptionHandler(AccessDeniedException.class)
  ResponseEntity<ApiResponse<Object>> handleAccessDenied(AccessDeniedException ex) {
    return ResponseEntity.status(HttpStatus.FORBIDDEN)
        .body(ApiResponse.error("forbidden", "Access denied"));
  }
  
  @ExceptionHandler(IllegalArgumentException.class)
  ResponseEntity<ApiResponse<Object>> handleIllegalArgument(IllegalArgumentException ex) {
    return ResponseEntity.status(HttpStatus.BAD_REQUEST)
        .body(ApiResponse.error("bad_request", ex.getMessage()));
  }

  @ExceptionHandler(Exception.class)
  ResponseEntity<ApiResponse<Object>> handleGeneric(Exception ex) {
    // Log unexpected errors with stack traces
    log.error("Unexpected error occurred", ex);
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
        .body(ApiResponse.error("internal_error", "Internal server error"));
  }
}
```

### Common Error Codes

| Error Code | HTTP Status | Usage |
|-----------|-------------|-------|
| `validation_error` | 400 | Request validation failed |
| `bad_request` | 400 | Malformed request or invalid parameters |
| `unauthorized` | 401 | Missing or invalid authentication |
| `forbidden` | 403 | Authenticated but not authorized |
| `not_found` | 404 | Resource not found |
| `conflict` | 409 | Duplicate entry or state conflict |
| `internal_error` | 500 | Unexpected server error |

### Detailed Error Response with Field-Level Details

For validation errors with multiple fields, you can extend the response:

```java
public record ValidationErrorDetail(
    String field,
    String message,
    String code) {}

public record ApiResponseWithDetails<T>(
    T data,
    String errorCode,
    String errorMessage,
    List<ValidationErrorDetail> details) {
  
  public static <T> ApiResponseWithDetails<T> success(T data) {
    return new ApiResponseWithDetails<>(data, null, null, null);
  }
  
  public static <T> ApiResponseWithDetails<T> validationError(
      String message, 
      List<ValidationErrorDetail> details) {
    return new ApiResponseWithDetails<>(null, "validation_error", message, details);
  }
}

@ExceptionHandler(MethodArgumentNotValidException.class)
ResponseEntity<ApiResponseWithDetails<Object>> handleValidationWithDetails(
    MethodArgumentNotValidException ex) {
  List<ValidationErrorDetail> details = ex.getBindingResult().getFieldErrors().stream()
      .map(e -> new ValidationErrorDetail(
          e.getField(),
          e.getDefaultMessage(),
          e.getCode()))
      .toList();
  
  return ResponseEntity.status(HttpStatus.BAD_REQUEST)
      .body(ApiResponseWithDetails.validationError(
          "Request validation failed",
          details));
}
```

## Caching

Requires `@EnableCaching` on a configuration class.

```java
@Service
public class MarketCacheService {
  private final MarketRepository repo;

  public MarketCacheService(MarketRepository repo) {
    this.repo = repo;
  }

  @Cacheable(value = "market", key = "#id")
  public Market getById(Long id) {
    return repo.findById(id)
        .map(Market::from)
        .orElseThrow(() -> new EntityNotFoundException("Market not found"));
  }

  @CacheEvict(value = "market", key = "#id")
  public void evict(Long id) {}
}
```

## Async Processing

Requires `@EnableAsync` on a configuration class.

```java
@Service
public class NotificationService {
  @Async
  public CompletableFuture<Void> sendAsync(Notification notification) {
    // send email/SMS
    return CompletableFuture.completedFuture(null);
  }
}
```

## Logging (SLF4J)

```java
@Service
public class ReportService {
  private static final Logger log = LoggerFactory.getLogger(ReportService.class);

  public Report generate(Long marketId) {
    log.info("generate_report marketId={}", marketId);
    try {
      // logic
    } catch (Exception ex) {
      log.error("generate_report_failed marketId={}", marketId, ex);
      throw ex;
    }
    return new Report();
  }
}
```

## Middleware / Filters

```java
@Component
public class RequestLoggingFilter extends OncePerRequestFilter {
  private static final Logger log = LoggerFactory.getLogger(RequestLoggingFilter.class);

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain filterChain) throws ServletException, IOException {
    long start = System.currentTimeMillis();
    try {
      filterChain.doFilter(request, response);
    } finally {
      long duration = System.currentTimeMillis() - start;
      log.info("req method={} uri={} status={} durationMs={}",
          request.getMethod(), request.getRequestURI(), response.getStatus(), duration);
    }
  }
}
```

## Pagination and Sorting

```java
PageRequest page = PageRequest.of(pageNumber, pageSize, Sort.by("createdAt").descending());
Page<Market> results = marketService.list(page);
```

## Error-Resilient External Calls

```java
public <T> T withRetry(Supplier<T> supplier, int maxRetries) {
  int attempts = 0;
  while (true) {
    try {
      return supplier.get();
    } catch (Exception ex) {
      attempts++;
      if (attempts >= maxRetries) {
        throw ex;
      }
      try {
        Thread.sleep((long) Math.pow(2, attempts) * 100L);
      } catch (InterruptedException ie) {
        Thread.currentThread().interrupt();
        throw ex;
      }
    }
  }
}
```

## Rate Limiting (Filter + Bucket4j)

**Security Note**: The `X-Forwarded-For` header is untrusted by default because clients can spoof it.
Only use forwarded headers when:
1. Your app is behind a trusted reverse proxy (nginx, AWS ALB, etc.)
2. You have registered `ForwardedHeaderFilter` as a bean
3. You have configured `server.forward-headers-strategy=NATIVE` or `FRAMEWORK` in application properties
4. Your proxy is configured to overwrite (not append to) the `X-Forwarded-For` header

When `ForwardedHeaderFilter` is properly configured, `request.getRemoteAddr()` will automatically
return the correct client IP from the forwarded headers. Without this configuration, use
`request.getRemoteAddr()` directly—it returns the immediate connection IP, which is the only
trustworthy value.

```java
@Component
public class RateLimitFilter extends OncePerRequestFilter {
  private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();

  /*
   * SECURITY: This filter uses request.getRemoteAddr() to identify clients for rate limiting.
   *
   * If your application is behind a reverse proxy (nginx, AWS ALB, etc.), you MUST configure
   * Spring to handle forwarded headers properly for accurate client IP detection:
   *
   * 1. Set server.forward-headers-strategy=NATIVE (for cloud platforms) or FRAMEWORK in
   *    application.properties/yaml
   * 2. If using FRAMEWORK strategy, register ForwardedHeaderFilter:
   *
   *    @Bean
   *    ForwardedHeaderFilter forwardedHeaderFilter() {
   *        return new ForwardedHeaderFilter();
   *    }
   *
   * 3. Ensure your proxy overwrites (not appends) the X-Forwarded-For header to prevent spoofing
   * 4. Configure server.tomcat.remoteip.trusted-proxies or equivalent for your container
   *
   * Without this configuration, request.getRemoteAddr() returns the proxy IP, not the client IP.
   * Do NOT read X-Forwarded-For directly—it is trivially spoofable without trusted proxy handling.
   */
  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain filterChain) throws ServletException, IOException {
    // Use getRemoteAddr() which returns the correct client IP when ForwardedHeaderFilter
    // is configured, or the direct connection IP otherwise. Never trust X-Forwarded-For
    // headers directly without proper proxy configuration.
    String clientIp = request.getRemoteAddr();

    Bucket bucket = buckets.computeIfAbsent(clientIp,
        k -> Bucket.builder()
            .addLimit(Bandwidth.classic(100, Refill.greedy(100, Duration.ofMinutes(1))))
            .build());

    if (bucket.tryConsume(1)) {
      filterChain.doFilter(request, response);
    } else {
      response.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
    }
  }
}
```

## Background Jobs

Use Spring’s `@Scheduled` or integrate with queues (e.g., Kafka, SQS, RabbitMQ). Keep handlers idempotent and observable.

## Observability

- Structured logging (JSON) via Logback encoder
- Metrics: Micrometer + Prometheus/OTel
- Tracing: Micrometer Tracing with OpenTelemetry or Brave backend

## Production Defaults

- Prefer constructor injection, avoid field injection
- Enable `spring.mvc.problemdetails.enabled=true` for RFC 7807 errors (Spring Boot 3+)
- Configure HikariCP pool sizes for workload, set timeouts
- Use `@Transactional(readOnly = true)` for queries
- Enforce null-safety via `@NonNull` and `Optional` where appropriate

**Remember**: Keep controllers thin, services focused, repositories simple, and errors handled centrally. Optimize for maintainability and testability.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skiluo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
