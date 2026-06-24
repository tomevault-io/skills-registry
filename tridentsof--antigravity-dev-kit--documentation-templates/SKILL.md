---
name: documentation-templates
description: name: documentation-templates Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: documentation-templates
description: Templates for technical documentation
---

# Documentation Templates

> Standard templates for project documentation.

---

## README Template

```markdown
# Project Name

Brief description.

## Quick Start

\`\`\`bash
# Installation
npm install

# Run
npm run dev
\`\`\`

## Features

- Feature 1
- Feature 2

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| API_URL | API endpoint | http://localhost |

## API Reference

See [API Documentation](./docs/api.md)

## Contributing

1. Fork the repo
2. Create feature branch
3. Submit PR

## License

MIT
```

---

## API Endpoint Template

```markdown
## GET /api/users/{id}

Get user by ID.

### Parameters

| Name | Type | Description |
|------|------|-------------|
| id | int | User ID |

### Response

\`\`\`json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com"
}
\`\`\`

### Errors

| Status | Description |
|--------|-------------|
| 404 | User not found |
```

---

## Architecture Doc Template

```markdown
# Architecture: [System Name]

## Overview

Brief description of the system.

## Components

| Component | Purpose |
|-----------|---------|
| API | REST endpoints |
| Database | Data storage |

## Data Flow

1. User action
2. API call
3. Database query
4. Response

## Decisions

| Decision | Rationale |
|----------|-----------|
| SQL Server | Existing infrastructure |
```

---

## When to Document

| ✅ Document | ❌ Skip |
|-------------|---------|
| Public APIs | Internal helpers |
| Complex logic | Self-documenting code |
| Architecture decisions | Obvious patterns |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
