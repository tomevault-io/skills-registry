---
name: rest-api-standards
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

## Universal Principles

1. **Spec-First Development** – OpenAPI contract **MUST** be authored (or
   updated) _before_ implementation code is merged.
2. **Versioning** – API major version **MUST** appear in the path
   (`/api/v{major}/…`) _and_ in the `info.version` field.
3. **Consistent Envelope** – Error responses **MUST** follow
   `{ code, message, data }`; success bodies **SHOULD** wrap data similarly.
4. **Security Schemes** – Auth **MUST** be declared with `type: http`,
   `scheme: bearer`, `bearerFormat: JWT`; scopes live under
   `components/securitySchemes`.
5. **Traceability** – `traceparent` and `baggage` headers **MUST** be documented
   under `components/headers` so clients propagate OpenTelemetry context.
6. **Pagination** – Collections **SHOULD** use `limit` / `cursor` parameters;
   responses **MUST** include `next` cursor link.
7. **Rate Limits** – Specs **MUST** declare `429` response and document
   `Retry-After` header semantics.
8. **API Linting** – PRs **MUST** pass the OpenAPI linter (`spectral` with
   company ruleset) at severity _error = 0_.
9. **Example Payloads** – Each schema **SHOULD** include at least one example;
   sensitive data is tokenised.
10. **Documentation** – Rendered docs (Redoc or Stoplight) **MUST** auto-publish
    on merge to `main`.

---

## New Code

11. **OpenAPI 3.0** – New APIs **MUST** target spec v3.0; earlier versions are
    prohibited.
12. **JSON Schema `$defs`** – Schema components **SHOULD** ref common `$defs`
    published by the Platform team to avoid duplication.

---

## Existing Code

13. **Legacy Swagger 2.0** – Existing Swagger specs **MUST** migrate to OpenAPI
    3.0+ when touched.
14. **Inline Definitions** – While migrating, inline schema refs are permissible
    _only_ if externalising would break consumers; file a debt entry.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
