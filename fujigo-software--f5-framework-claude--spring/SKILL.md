---
name: spring-skills
description: Spring Boot framework patterns, best practices, and implementation guides Use when this capability is needed.
metadata:
  author: fujigo-software
---

# Spring Boot Skills

Java enterprise framework for building production-ready applications.

## Sub-Skills

### Architecture
- [service-layer.md](architecture/service-layer.md) - Service layer patterns
- [controller-patterns.md](architecture/controller-patterns.md) - REST controller patterns
- [dependency-injection.md](architecture/dependency-injection.md) - DI patterns

### Database
- [spring-data-jpa.md](database/spring-data-jpa.md) - Spring Data JPA patterns
- [jpa-patterns.md](database/jpa-patterns.md) - JPA entity patterns
- [transactions.md](database/transactions.md) - Transaction management
- [flyway-migrations.md](database/flyway-migrations.md) - Database migrations

### Security
- [spring-security.md](security/spring-security.md) - Spring Security configuration
- [jwt-auth.md](security/jwt-auth.md) - JWT authentication
- [oauth2.md](security/oauth2.md) - OAuth2 integration
- [method-security.md](security/method-security.md) - Method-level security

### Validation
- [bean-validation.md](validation/bean-validation.md) - Bean validation
- [custom-validators.md](validation/custom-validators.md) - Custom validators

### Error Handling
- [problem-details.md](error-handling/problem-details.md) - RFC 7807 Problem Details
- [global-exception.md](error-handling/global-exception.md) - Global exception handling

### Testing
- [unit-testing.md](testing/unit-testing.md) - Unit testing with JUnit
- [integration-testing.md](testing/integration-testing.md) - Integration testing
- [mockito-patterns.md](testing/mockito-patterns.md) - Mockito patterns

### Performance
- [caching.md](performance/caching.md) - Spring Cache abstraction

## Detection
Auto-detected when project contains:
- `pom.xml` or `build.gradle`
- `@SpringBootApplication` annotation
- `org.springframework.boot` packages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fujigo-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
