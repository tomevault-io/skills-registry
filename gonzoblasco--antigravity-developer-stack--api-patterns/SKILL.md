---
name: api-patterns
description: Use cuando diseñes APIs, elijas entre REST/GraphQL/tRPC, definas formatos de respuesta, planifiques versionado, o implementes rate limiting. Keywords: API design, REST, GraphQL, tRPC, OpenAPI, versioning, pagination, rate limiting, authentication, status codes. Use when this capability is needed.
metadata:
  author: gonzoblasco
---

# API Patterns

> API design principles and decision-making for 2025.
> **Learn to THINK, not copy fixed patterns.**

## 🎯 Selective Reading Rule

**Read ONLY files relevant to the request!** Check the content map, find what you need.

---

## 📑 Content Map

| File                             | Description                                 | When to Read           |
| -------------------------------- | ------------------------------------------- | ---------------------- |
| `references/api-style.md`        | REST vs GraphQL vs tRPC decision tree       | Choosing API type      |
| `references/rest.md`             | Resource naming, HTTP methods, status codes | Designing REST API     |
| `references/response.md`         | Envelope pattern, error format, pagination  | Response structure     |
| `references/graphql.md`          | Schema design, when to use, security        | Considering GraphQL    |
| `references/trpc.md`             | TypeScript monorepo, type safety            | TS fullstack projects  |
| `references/versioning.md`       | URI/Header/Query versioning                 | API evolution planning |
| `references/auth.md`             | JWT, OAuth, Passkey, API Keys               | Auth pattern selection |
| `references/rate-limiting.md`    | Token bucket, sliding window                | API protection         |
| `references/documentation.md`    | OpenAPI/Swagger best practices              | Documentation          |
| `references/security-testing.md` | OWASP API Top 10, auth/authz testing        | Security audits        |

---

## 🚀 Quick Decision

```
Who are the API consumers?
│
├── Public API / Multiple platforms ────→ REST + OpenAPI
├── Complex data / Multiple frontends ──→ GraphQL
├── TypeScript monorepo ────────────────→ tRPC
├── Real-time / Event-driven ───────────→ WebSocket + AsyncAPI
└── Internal microservices ─────────────→ gRPC or REST
```

> **First question ALWAYS:** ¿Quién va a consumir esta API?

---

## 🔗 Related Skills

| Need               | Skill                           |
| ------------------ | ------------------------------- |
| API implementation | `@[skills/backend-development]` |
| Data structure     | `@[skills/database-design]`     |
| Security details   | `@[skills/security-hardening]`  |

---

## ✅ Decision Checklist

Before designing an API:

- [ ] **Asked user about API consumers?**
- [ ] **Chosen API style for THIS context?** (REST/GraphQL/tRPC)
- [ ] **Defined consistent response format?**
- [ ] **Planned versioning strategy?**
- [ ] **Considered authentication needs?**
- [ ] **Planned rate limiting?**
- [ ] **Documentation approach defined?**

---

## ⚠️ Racionalizaciones Comunes

| Excusa                      | Realidad                                                              |
| --------------------------- | --------------------------------------------------------------------- |
| "Siempre usamos REST"       | Evalúa consumidores primero - tRPC o GraphQL podrían ser mejores      |
| "GraphQL es overkill"       | Para apps con datos complejos, reduce overfetching significativamente |
| "No necesitamos versionado" | Los clientes romperán sin aviso cuando hagas breaking changes         |
| "Rate limiting después"     | Implementar desde día 1 es 10x más fácil que retrofitting             |
| "Los errores ya se manejan" | Sin formato consistente, el frontend sufre                            |

---

## ❌ Anti-Patterns

**NUNCA:**

- Defaults a REST sin evaluar contexto
- Verbos en endpoints REST (`/getUsers` → `/users`)
- Formatos de respuesta inconsistentes
- Exponer errores internos al cliente
- Saltear rate limiting

**SIEMPRE:**

- Elegir estilo API basado en consumidores
- Preguntar requisitos del cliente
- Documentar exhaustivamente
- Usar status codes apropiados

---

## Script

| Script                     | Purpose                 | Command                                          |
| -------------------------- | ----------------------- | ------------------------------------------------ |
| `scripts/api_validator.py` | API endpoint validation | `python scripts/api_validator.py <project_path>` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gonzoblasco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
