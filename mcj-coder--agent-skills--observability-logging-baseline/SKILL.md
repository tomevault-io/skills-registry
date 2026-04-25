---
name: observability-logging-baseline
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Observability Logging Baseline

## Intent

Establish a consistent, secure logging and tracing baseline that enables
end-to-end correlation, supports operational diagnostics, and avoids sensitive
information exposure.

---

## When to Use

- Introducing or standardizing structured logging.
- Enabling distributed tracing across services.
- Migrating a repo to required logging/tracing standards.
- Defining log level policies and correlation requirements.

---

## Precondition Failure Signal

- Logs are unstructured or inconsistent across components.
- Distributed traces cannot be followed end-to-end.
- Correlation-Id is missing from logs or spans.
- Log levels are used inconsistently or too verbosely in non-development environments.
- Logs contain PII, secrets, or sensitive data.

---

## Postcondition Success Signal

- Structured logs are implemented consistently across components.
- Distributed tracing is enabled and trace context is propagated.
- Correlation-Id is propagated and attached to logs and spans.
- Log level policy is enforced (INFO+ in non-development; non-app sources WARNING+).
- Logging content follows OWASP guidance and avoids PII/secrets.

---

## Process

1. **Source Review**: Inspect existing logging/tracing configuration and data
   flows for gaps in correlation, structure, and security hygiene.
2. **Design**: Define the correlation header, log levels, and tracing
   propagation standards (keep `Correlation-Id` distinct from W3C trace context).
3. **Implementation**:
   - Implement structured logging across application code.
   - Enable distributed tracing and propagate W3C trace context headers.
   - Inject the Correlation-Id into logs and spans without replacing W3C trace
     context headers.
   - Ensure API definitions include Correlation-Id as an optional header.
   - Apply environment log level defaults (INFO+ in non-development;
     non-application sources WARNING+).
4. **Verification**: Confirm that a single request can be traced through logs
   and spans using Correlation-Id and trace context headers.
5. **Documentation**: Record logging/tracing expectations, headers, and
   redaction guidance in repo documentation.
6. **Review**: Security Reviewer, Lead Developer, and Platform/DevOps review for
   data safety, operational utility, and enforcement feasibility.

---

## Example Test / Validation

- A request produces structured logs and spans with the same Correlation-Id.
- A trace can be reconstructed using W3C trace context without Correlation-Id
  collisions.
- Seeded PII or secrets do not appear in logs.

---

## Common Red Flags / Guardrail Violations

- Logging raw request bodies or authentication tokens.
- Using Correlation-Id in place of W3C trace context headers.
- INFO-level logging for every request in non-development environments.
- Logging from framework/runtime sources at INFO or lower in production.

---

## Recommended Review Personas

- **Security Reviewer** - validates OWASP alignment and data minimization.
- **Lead Developer** - validates log level policy and meaningful logging.
- **Platform/DevOps Engineer** - validates tracing propagation and operability.
- **Tech Lead** - validates cross-service consistency and standards alignment.

---

## Skill Priority

P1 - Quality & Correctness
(Escalate to P0 when logging intersects with security incidents or compliance.)

---

## Conflict Resolution Rules

- Security and privacy constraints override observability convenience.
- If performance constraints require reduced logging, preserve policy and
  document exceptions with explicit approval.
- Trace context propagation must remain standards-compliant.

---

## Conceptual Dependencies

- automated-standards-enforcement
- quality-gate-enforcement
- static-analysis-security
- local-dev-experience
- documentation-as-code

---

## Classification

Core
Operational

---

## Notes

Log level policy:

- INFO: only for meaningful, completed operations in application code.
- WARNING: expected or recoverable error cases.
- ERROR: unexpected exceptions that do not crash the application.
- CRITICAL/FATAL: failures that prevent the application from continuing.

Correlation guidance:

- Correlation-Id must be included in logs and spans.
- UI flows generate a Correlation-Id per user action.
- Non-UI services generate a Correlation-Id on the first request.
- Correlation-Id is an optional API header and does not replace W3C trace
  context headers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
