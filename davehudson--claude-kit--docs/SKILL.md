---
name: docs
description: Documentation patterns for technical writing. Auto-loads when creating API docs, README files, JSDoc comments, or user guides. Use when this capability is needed.
metadata:
  author: davehudson
---

Documentation specialist for creating clear, useful technical documentation.

---

## Documentation Types

### 1. Code Documentation (JSDoc/TSDoc)

**When to document:**
- Public APIs and exported functions
- Complex algorithms or business logic
- Non-obvious parameters or return values
- Deprecations and migrations

**When NOT to document:**
- Self-explanatory code
- Private implementation details
- Obvious getters/setters

### 2. README Files

Essential sections:
- **Title & Description** - What is this?
- **Installation** - How to set up
- **Usage** - Quick start example
- **API** - Reference for public interface
- **Contributing** - How to help (if OSS)

### 3. API Documentation

- Endpoint descriptions
- Request/response examples
- Error codes and handling
- Authentication requirements
- Rate limits

### 4. Architecture Docs

- System overview diagrams
- Component relationships
- Data flow explanations
- Decision records (ADRs)

---

## JSDoc/TSDoc Patterns

### Functions
```typescript
/**
 * Calculates the total price including tax and discounts.
 *
 * @param items - Cart items to calculate
 * @param taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @param couponCode - Optional discount code
 * @returns Total price in cents
 * @throws {InvalidCouponError} If coupon code is invalid
 *
 * @example
 * const total = calculateTotal(items, 0.08, 'SAVE10');
 */
function calculateTotal(
  items: CartItem[],
  taxRate: number,
  couponCode?: string
): number
```

### Components
```typescript
/**
 * Displays a user avatar with optional status indicator.
 *
 * @example
 * <Avatar user={currentUser} size="lg" showStatus />
 */
interface AvatarProps {
  /** User object containing name and image URL */
  user: User;
  /** Size variant */
  size?: 'sm' | 'md' | 'lg';
  /** Show online/offline status dot */
  showStatus?: boolean;
}
```

### Deprecations
```typescript
/**
 * @deprecated Use `fetchUserById` instead. Will be removed in v3.0.
 * @see fetchUserById
 */
function getUser(id: string): Promise<User>
```

---

## README Template

```markdown
# Project Name

Brief description of what this project does.

## Installation

\`\`\`bash
bun add project-name
\`\`\`

## Quick Start

\`\`\`typescript
import { something } from 'project-name';

const result = something();
\`\`\`

## API

### `functionName(param)`

Description of what it does.

**Parameters:**
- `param` (type) - Description

**Returns:** type - Description

## Configuration

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| option1 | string | 'default' | What it does |

## License

MIT
```

---

## API Documentation Template

```markdown
## Endpoint Name

`POST /api/resource`

Brief description.

### Authentication

Requires Bearer token.

### Request

\`\`\`json
{
  "field": "value"
}
\`\`\`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| field | string | Yes | What it is |

### Response

**Success (200)**
\`\`\`json
{
  "id": "123",
  "created": "2024-01-01T00:00:00Z"
}
\`\`\`

**Errors**
| Code | Description |
|------|-------------|
| 400 | Invalid input |
| 401 | Unauthorized |
| 404 | Resource not found |
```

---

## Writing Guidelines

### Be Concise
```markdown
# BAD
This function is used to retrieve the user from the database
using their unique identifier which is passed as a parameter.

# GOOD
Fetches a user by ID.
```

### Use Active Voice
```markdown
# BAD
The configuration file should be created in the root directory.

# GOOD
Create the configuration file in the root directory.
```

### Show, Don't Just Tell
```markdown
# BAD
The function accepts various options.

# GOOD
\`\`\`typescript
fetchData({
  timeout: 5000,
  retries: 3,
  cache: true
});
\`\`\`
```

### Keep Examples Runnable
- Test all code examples
- Use realistic values
- Include necessary imports
- Show expected output

---

## Documentation Mindset

- **Write for the reader** - Not for yourself
- **Assume minimal context** - New developers exist
- **Keep it maintained** - Outdated docs are worse than none
- **Examples are essential** - Show, don't just tell
- **Less is more** - Don't document the obvious

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davehudson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
