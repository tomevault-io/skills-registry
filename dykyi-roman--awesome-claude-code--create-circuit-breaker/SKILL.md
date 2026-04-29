---
name: create-circuit-breaker
description: Generates Circuit Breaker pattern for PHP 8.4. Creates resilience component protecting against cascading failures with state management, fallback support, and metrics. Includes unit tests.
metadata:
  author: dykyi-roman
---

# Circuit Breaker Generator

Creates Circuit Breaker pattern infrastructure for resilience and fault tolerance.

## When to Use

| Scenario | Example |
|----------|---------|
| External service calls | API integrations, payment gateways |
| Database connections | Prevent connection exhaustion |
| Cascading failures | Stop failure propagation |
| Service degradation | Graceful fallback when service unavailable |

## Component Characteristics

### CircuitState Enum
- **Closed**: Normal operation, requests pass through
- **Open**: Failing, requests rejected immediately
- **HalfOpen**: Testing recovery, limited requests allowed

### CircuitBreaker
- Wraps external service calls
- Tracks failures and successes
- Automatic state transitions
- Configurable thresholds and timeouts

### CircuitBreakerConfig
- Failure threshold count
- Success threshold for recovery
- Open state timeout duration

---

## Generation Process

### Step 1: Generate Core Components

**Path:** `src/Infrastructure/Resilience/CircuitBreaker/`

1. `CircuitState.php` — Enum with state transitions
2. `CircuitBreakerConfig.php` — Configuration value object
3. `CircuitBreakerException.php` — Exception for open circuit

### Step 2: Generate Circuit Breaker

**Path:** `src/Infrastructure/Resilience/CircuitBreaker/`

1. `CircuitBreaker.php` — Main implementation with state management

### Step 3: Generate Factory and Registry

**Path:** `src/Infrastructure/Resilience/CircuitBreaker/`

1. `CircuitBreakerFactory.php` — Creates configured breakers
2. `CircuitBreakerRegistry.php` — Per-service breaker management

### Step 4: Generate Tests

1. `CircuitStateTest.php` — State transition tests
2. `CircuitBreakerTest.php` — Breaker behavior tests

---

## File Placement

| Component | Path |
|-----------|------|
| All Classes | `src/Infrastructure/Resilience/CircuitBreaker/` |
| Unit Tests | `tests/Unit/Infrastructure/Resilience/CircuitBreaker/` |

---

## Naming Conventions

| Component | Pattern | Example |
|-----------|---------|---------|
| State Enum | `CircuitState` | `CircuitState` |
| Config | `CircuitBreakerConfig` | `CircuitBreakerConfig` |
| Main Class | `CircuitBreaker` | `CircuitBreaker` |
| Factory | `CircuitBreakerFactory` | `CircuitBreakerFactory` |
| Registry | `CircuitBreakerRegistry` | `CircuitBreakerRegistry` |
| Exception | `CircuitBreakerException` | `CircuitBreakerException` |
| Test | `{ClassName}Test` | `CircuitBreakerTest` |

---

## Quick Template Reference

### CircuitState

```php
enum CircuitState: string
{
    case Closed = 'closed';
    case Open = 'open';
    case HalfOpen = 'half_open';

    public function allowsRequest(): bool;
    public function canTransitionTo(self $next): bool;
}
```

### CircuitBreakerConfig

```php
final readonly class CircuitBreakerConfig
{
    public function __construct(
        public int $failureThreshold = 5,
        public int $successThreshold = 3,
        public int $openTimeoutSeconds = 30,
        public int $halfOpenMaxAttempts = 3
    ) {}

    public static function default(): self;
    public static function aggressive(): self;
    public static function lenient(): self;
}
```

### CircuitBreaker

```php
final class CircuitBreaker
{
    public function execute(callable $operation, ?callable $fallback = null): mixed;
    public function canExecute(): bool;
    public function getState(): CircuitState;
    public function forceOpen(): void;
    public function forceClose(): void;
}
```

---

## Usage Example

```php
$breaker = $circuitBreakers->get('payment-gateway');

try {
    $result = $breaker->execute(
        operation: fn() => $paymentClient->charge($request),
        fallback: fn() => PaymentResult::deferred($request->id)
    );
} catch (CircuitBreakerException $e) {
    // Circuit is open
    return PaymentResult::serviceUnavailable($request->id);
}
```

---

## State Transitions

```
CLOSED ─────failure threshold reached────→ OPEN
   ↑                                          │
   │                                          │ timeout elapsed
   │                                          ↓
success threshold reached              HALF-OPEN
   └────────────────────────────────────────┘
                        │
            failure in half-open
                        │
                        ↓
                      OPEN
```

---

## Anti-patterns to Avoid

| Anti-pattern | Problem | Solution |
|--------------|---------|----------|
| Global Circuit Breaker | One breaker for all services | Per-service breakers |
| No Fallback | Hard failure on open | Provide fallback strategy |
| Immediate Retry | Hammering failed service | Use timeout before HalfOpen |
| No Metrics | Can't monitor health | Log state transitions |
| Static Thresholds | Can't tune per service | Configurable per service |
| No Manual Override | Can't force open/close | Add force methods |

---

## References

For complete PHP templates and examples, see:
- `references/templates.md` — CircuitState, CircuitBreakerConfig, CircuitBreaker, Factory, Registry templates
- `references/examples.md` — HTTP client, payment gateway examples and tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dykyi-roman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
