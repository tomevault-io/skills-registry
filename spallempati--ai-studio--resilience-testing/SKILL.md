---
name: resilience-testing
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Validate Data Flow Resilience via Fault-Tolerant Integration Tests

## Description

This rule promotes resilience testing by simulating upstream/downstream failures
in integration tests. Tests should assert that the system under test responds
with fallback behavior (e.g., retries, circuit breakers, failover logs) rather
than cascading failure or unhandled exceptions.

## Purpose

To ensure that mission-critical services remain operational even when
dependencies fail. This improves availability, supports fault isolation, and
aligns with modern resilience engineering practices.

## Scope

- Critical path services and workflows
- Integration tests involving third-party APIs, microservice dependencies,
  databases, or queues
- Applies to all developers and system testers
- Enforced via CI environments using mock failure injection or service
  virtualization

## SDLC Integration

- **Planning**: Failure scenarios are captured as acceptance criteria
- **Analysis**: Identifies SLAs and recovery thresholds
- **Design**: Uses circuit breakers, retry policies, and timeouts
- **Development**: Adds negative-path tests to simulate failure
- **Testing**: Validates fallback logic in real environments
- **Deployment**: Prevents ungraceful degradation in production
- **Maintenance**: Regular review of resilience patterns across services

## Standards

### Failure Scenario Testing

- Integration tests **SHOULD** simulate at least one failure scenario per
  critical service
- Systems **MUST** retry, degrade gracefully, or return default values on
  dependency failure
- Circuit breakers and timeout logic **SHOULD** be test-covered
- Retry attempts **MUST NOT** exceed safe retry thresholds to avoid service
  amplification

## Actionable Metrics

| Metric                   | Target Value              | Measurement Method              | Enforcement Level |
| ------------------------ | ------------------------- | ------------------------------- | ----------------- |
| Resilience test presence | ≥ 1 per critical service  | Test tag or folder scan         | **SHOULD**        |
| Response under failure   | No crash, fallback logged | Log assertion in test framework | **MUST**          |
| Retry logic tested       | Yes                       | Observed call count or delay    | **SHOULD**        |

## Implementation

### Configuration Requirements

- Use test doubles or service virtualization to simulate HTTP 503, timeouts, or
  dropped responses
- Assert application logs and retry/backoff mechanisms

#### Example: Correct Implementation (Java)

```java
@Test
void retriesOnceOnBilling503() {
    wireMockServer.stubFor(post(urlEqualTo("/billing"))
        .willReturn(aResponse().withStatus(503)));

    checkoutService.checkout(user);

    verify(billingClient, times(2)).charge(any());
    assertTrue(logsContain("Billing retry triggered"));
}
```

#### Example: Correct Implementation (C#/.NET with Polly)

```csharp
// Configure resilience policy with Polly
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .OrResult<HttpResponseMessage>(r => r.StatusCode == HttpStatusCode.ServiceUnavailable)
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)));

// Test resilience behavior
[Fact]
public async Task Checkout_BillingServiceUnavailable_RetriesAndSucceeds()
{
    // Arrange
    var callCount = 0;
    A.CallTo(() => _billingClient.ChargeAsync(A<ChargeRequest>._))
        .ReturnsLazily(() =>
        {
            callCount++;
            if (callCount < 3)
                throw new HttpRequestException("Service unavailable");
            return Task.FromResult(new ChargeResponse { Success = true });
        });

    // Act
    var result = await _checkoutService.CheckoutAsync(user);

    // Assert
    Assert.True(result.Success);
    Assert.Equal(3, callCount); // Retried twice before succeeding
}

[Fact]
public async Task Checkout_BillingServiceDown_CircuitBreakerOpens()
{
    // Arrange - All calls fail
    A.CallTo(() => _billingClient.ChargeAsync(A<ChargeRequest>._))
        .ThrowsAsync(new HttpRequestException("Service unavailable"));

    // Act & Assert
    await Assert.ThrowsAsync<BrokenCircuitException>(
        () => _checkoutService.CheckoutAsync(user));
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
