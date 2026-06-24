---
name: documentation-best-practices
description: Use when creating and maintaining project documentation, writing README files, documenting APIs, establishing documentation standards, and building knowledge bases. Includes Markdown, ADRs, API docs, and documentation automation. Based on software documentation certification standards and technical writing best practices.
metadata:
  author: Omar-Obando
---

# Documentation Best Practices Skill — Comprehensive Documentation Standards

## Overview

This skill provides comprehensive guidance for creating and maintaining project documentation, writing README files, documenting APIs, establishing documentation standards, and building knowledge bases. It includes Markdown, ADRs, API docs, and documentation automation. Based on software documentation certification standards and technical writing best practices.

## When to Use

**Use this skill when:**

- Creating project documentation for new projects
- Writing README files for repositories
- Documenting APIs (REST, GraphQL, gRPC)
- Establishing documentation standards across teams
- Building knowledge bases for organizations
- Creating Architecture Decision Records (ADRs)
- Writing API documentation with OpenAPI/Swagger
- Setting up documentation automation
- Creating documentation templates and styles
- Building documentation websites (Docusaurus, MkDocs)
- Implementing documentation versioning
- Creating documentation for different audiences
- Setting up documentation review processes
- Implementing documentation quality gates
- Creating documentation metrics and KPIs
- Building documentation migration strategies
- Setting up documentation monitoring
- Creating documentation training materials
- Implementing documentation security
- Creating documentation compliance documentation

**Do NOT use this skill when:**

- Writing code implementation (use developer skill)
- Designing system architecture (use architecture-patterns skill)
- Writing tests (use testing-strategy skill)
- Implementing features (use implementer agent)

## Documentation Types

### 1. README Files

Every project, module, and package needs a README.

````markdown
# Project Name

**Version:** 1.0.0  
**Status:** Active  
**License:** MIT

## Overview

Brief description of what this project does and why it exists.

## Features

- Feature 1
- Feature 2
- Feature 3

## Quick Start

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Run tests
npm test
```
````

## Project Structure

```
project/
├── src/           # Source code
├── tests/         # Test files
├── docs/          # Documentation
└── scripts/       # Automation scripts
```

## Configuration

| Option   | Type    | Default | Description       |
| -------- | ------- | ------- | ----------------- |
| debug    | boolean | false   | Enable debug mode |
| port     | number  | 3000    | Server port       |
| logLevel | string  | info    | Logging level     |

## API Reference

See [API Documentation](docs/api.md) for complete API reference.

## Contributing

See [Contributing Guide](CONTRIBUTING.md) for details.

## License

MIT

````

### 2. API Documentation

Document all public APIs.

```markdown
# API Documentation

## Base URL

````

https://api.example.com/v1

```

## Authentication

All endpoints require authentication via Bearer token.

```

Authorization: Bearer <token>

````

## Endpoints

### GET /api/v1/users

Retrieve a list of users.

**Query Parameters:**
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20)
- `sort` (optional): Sort field (default: name)
- `order` (optional): Sort order (asc/desc, default: asc)

**Response:**
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "string",
      "email": "string"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}
````

### POST /api/v1/users

Create a new user.

**Request Body:**

```json
{
  "name": "string (required)",
  "email": "string (required)",
  "password": "string (required)",
  "role": "string (optional, default: user)"
}
```

**Response:**

```json
{
  "id": "uuid",
  "name": "string",
  "email": "string",
  "role": "string",
  "createdAt": "ISO8601"
}
```

## Error Responses

| Status | Code             | Description               |
| ------ | ---------------- | ------------------------- |
| 400    | INVALID_INPUT    | Request validation failed |
| 401    | UNAUTHORIZED     | Authentication required   |
| 403    | FORBIDDEN        | Insufficient permissions  |
| 404    | NOT_FOUND        | Resource not found        |
| 422    | VALIDATION_ERROR | Business rule violation   |
| 500    | SERVER_ERROR     | Internal server error     |

````

### 3. Architecture Decision Records (ADRs)

Document architecture decisions.

```markdown
# ADR-001: Database Technology Selection

**Date:** 2024-01-15
**Status:** Accepted

## Context

We need to choose a database for our ERP system. The system requires:
- ACID compliance
- Complex queries
- High availability
- Scalability

## Options Considered

### PostgreSQL

**Pros:**
- ACID compliant
- Rich query capabilities
- Strong community
- Good performance

**Cons:**
- Scaling requires more effort
- Learning curve for complex features

### MongoDB

**Pros:**
- Easy scaling
- Flexible schema
- Good for document-based data

**Cons:**
- No ACID compliance (until 4.0)
- Complex queries are harder
- Memory usage

### MySQL

**Pros:**
- Well-understood
- Good performance
- Strong community

**Cons:**
- Limited JSON support
- Scaling challenges

## Decision

**We chose PostgreSQL.**

### Rationale

- ACID compliance is critical for ERP
- Complex queries are common in ERP
- Performance is acceptable
- Community support is strong

### Implementation

- Use PostgreSQL 15
- Enable logical replication
- Set up read replicas
- Implement connection pooling

## Consequences

### Positive

- Data integrity guaranteed
- Complex queries supported
- Good performance for our use case

### Negative

- Scaling requires more effort
- Learning curve for team
- More complex setup

## Next Steps

- Set up production database
- Migrate existing data
- Document database schema
- Train team on PostgreSQL

## References

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Database Selection Study](https://example.com/study)
````

### 4. Module Documentation

Document each business module.

```markdown
# Module Name

**Version:** 1.0.0  
**Domain:** business-domain  
**Bounded Context:** context-name

## Overview

Brief description of the module's purpose and business value.

## Domain Model

### Entities

- `EntityName` - Description
- `EntityName` - Description

### Value Objects

- `ValueObjectName` - Description
- `ValueObjectName` - Description

## API

### Endpoints

- `GET /module-name/` - Description
- `POST /module-name/` - Description

## Integration

### Dependencies

- `module-a` - Purpose
- `module-b` - Purpose

### Dependencies On

- None

## Testing

- Unit tests: `tests/unit/`
- Integration tests: `tests/integration/`

## Documentation

- `API.md` - API documentation
- `DECISIONS.md` - Architecture decisions
- `TESTING.md` - Testing strategy
```

## Documentation Standards

### Markdown Style

```markdown
# Headers (PascalCase)

## Section Headers

### Subsections

- Bullet lists
- Numbered lists

`code blocks`

| Tables | Are    | Good |
| ------ | ------ | ---- |
| Use    | them   | for  |
| Data   | tables |      |

> Quotes for emphasis
```

### File Naming

| File Type    | Convention      | Example         |
| ------------ | --------------- | --------------- |
| README       | README.md       | README.md       |
| API Docs     | API.md          | API.md          |
| ADR          | ADR-001.md      | ADR-001.md      |
| Testing      | TESTING.md      | TESTING.md      |
| Contributing | CONTRIBUTING.md | CONTRIBUTING.md |

### Documentation Locations

```
docs/
├── README.md                    # Main README
├── architecture/
│   ├── decisions/              # ADRs
│   │   ├── ADR-001.md
│   │   └── ADR-002.md
│   └── patterns/               # Architecture patterns
│       ├── layered-architecture.md
│       └── ddd.md
├── api/                        # API documentation
│   ├── v1/
│   │   ├── endpoints/
│   │   └── guides/
│   └── README.md
├── modules/                    # Module documentation
│   ├── inventory/
│   │   ├── README.md
│   │   ├── API.md
│   │   └── DECISIONS.md
│   └── sales/
│       └── ...
├── contributing.md             # Contributing guide
├── code-of-conduct.md          # Code of conduct
└── deployment.md               # Deployment guide
```

## Documentation Maintenance

### When to Update

- After any architectural decision
- When adding new APIs
- When changing behavior
- When fixing bugs that change behavior
- When onboarding new team members

### Documentation Review

- Review PRs include documentation changes
- Documentation is checked in code review
- Outdated documentation is flagged

## Documentation Tools

### Static Site Generators

| Tool       | Purpose                |
| ---------- | ---------------------- |
| Docusaurus | React-based docs site  |
| MkDocs     | Python-based docs site |
| Sphinx     | Python API docs        |
| JSDoc      | JavaScript API docs    |

### API Documentation

| Tool            | Purpose            |
| --------------- | ------------------ |
| Swagger/OpenAPI | REST API docs      |
| Postman         | API testing & docs |
| GraphQL Schema  | GraphQL docs       |

### Documentation Automation

| Tool          | Purpose              |
| ------------- | -------------------- |
| TypeDoc       | TypeScript API docs  |
| Swagger UI    | Interactive API docs |
| Read the Docs | Hosted documentation |

## Common Anti-Patterns

### ❌ Bad: Outdated Documentation

````markdown
# ❌ BAD: Outdated example

```typescript
// This example is outdated
const result = oldFunction();
```
````

# ✅ GOOD: Updated example

```typescript
// Current example
const result = newFunction();
```

````

### ❌ Bad: No Examples

```markdown
# ❌ BAD: No examples
The function processes data.

# ✅ GOOD: With examples
The function processes data:

```typescript
const result = processor.process(data);
````

````

### ❌ Bad: Too Much Detail

```markdown
# ❌ BAD: Too much detail
1. Open the file
2. Read the content
3. Parse the JSON
4. Validate the schema
5. ...

# ✅ GOOD: Appropriate detail
The function reads and parses a JSON file.
````

### ❌ Bad: Missing Status

```markdown
# ❌ BAD: No status

# Project Name

Some description...

# ✅ GOOD: With status

# Project Name

**Version:** 1.0.0  
**Status:** Active  
**License:** MIT

Some description...
```

## Documentation Quality Checklist

```markdown
## Documentation Quality Checklist

### README Files

- [ ] Clear project description
- [ ] Quick start instructions
- [ ] Project structure overview
- [ ] Configuration options
- [ ] API reference link
- [ ] Contributing link
- [ ] License information

### API Documentation

- [ ] Base URL specified
- [ ] Authentication explained
- [ ] All endpoints documented
- [ ] Request/response examples
- [ ] Error responses documented
- [ ] Rate limiting information

### ADRs

- [ ] Clear context
- [ ] Options considered
- [ ] Decision documented
- [ ] Rationale explained
- [ ] Consequences listed
- [ ] Next steps defined

### Module Documentation

- [ ] Module purpose clear
- [ ] Domain model documented
- [ ] API endpoints listed
- [ ] Integration points defined
- [ ] Testing strategy explained
```

## Real-World Impact

**Before this skill:**

- No documentation
- Outdated docs
- Hard to onboard
- Unknown APIs

**After this skill:**

- Comprehensive documentation
- Up-to-date docs
- Easy onboarding
- Clear APIs

## Cross-References

- **`api-design`** - For API documentation standards
- **`designing-architecture`** - For architecture documentation
- **`product-owner`** - For user story documentation

## References

- [Google Developer Documentation Style Guide](https://developers.google.com/style)
- [Microsoft Writing Style Guide](https://docs.microsoft.com/en-us/style-guide/)
- [Docusaurus Documentation](https://docusaurus.io/)
- [Markdown Guide](https://www.markdownguide.org/)

---
> Source: [Omar-Obando/qwen-orchestrator](https://github.com/Omar-Obando/qwen-orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
