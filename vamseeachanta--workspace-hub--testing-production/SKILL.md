---
name: testing-production
description: Production validation specialist ensuring applications are fully implemented Use when this capability is needed.
metadata:
  author: vamseeachanta
---

# Testing Production

## Quick Start

```bash
# Scan for incomplete implementations
grep -r "mock\|fake\|stub\|TODO\|FIXME" src/ --exclude-dir=__tests__

# Run production validation tests
npm run test:production
npm run test:e2e

# Validate against real services
npm run test:integration -- --env=staging
```

## When to Use

- Before deploying to production
- After completing TDD implementation phase
- To verify no mock implementations remain in production code
- To test against real databases, APIs, and infrastructure
- To validate performance under realistic load
- To confirm security measures are properly implemented

## Prerequisites

- Completed unit/integration test suite
- Access to staging environment with real services
- Environment variables for real service connections
- Load testing tools (if validating performance)

## References

- [Continuous Delivery](https://continuousdelivery.com/)
- [Testing in Production](https://launchdarkly.com/blog/testing-in-production/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

## Version History

- **1.0.0** (2026-01-02): Initial release - converted from production-validator agent

## Sub-Skills

- [Configuration](configuration/SKILL.md)
- [Example 1: Complete Validation Suite (+2)](example-1-complete-validation-suite/SKILL.md)
- [Example 4: Security Validation](example-4-security-validation/SKILL.md)
- [Real Data Usage (+3)](real-data-usage/SKILL.md)

## Sub-Skills

- [Execution Checklist](execution-checklist/SKILL.md)
- [Error Handling](error-handling/SKILL.md)

## Sub-Skills

- [Production Validation vs Unit Testing (+1)](production-validation-vs-unit-testing/SKILL.md)
- [1. Implementation Completeness Check (+2)](1-implementation-completeness-check/SKILL.md)
- [Metrics & Success Criteria](metrics-success-criteria/SKILL.md)
- [MCP Tools (+2)](mcp-tools/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vamseeachanta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
