---
name: documentation
description: Write clear, useful documentation Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Documentation

Write documentation that helps users and developers understand and use your code.

## JSDoc Comments

### Rules

- ✅ DO: Document all public APIs
- ✅ DO: Include parameter types and descriptions
- ✅ DO: Document return values and thrown errors
- ✅ DO: Add examples for complex functions
- ❌ DON'T: Document obvious code
- ❌ DON'T: Let docs get out of sync with code

### Examples

````typescript
/**
 * Calculates the total price including tax and discounts.
 *
 * @param items - Array of cart items with price and quantity
 * @param options - Calculation options
 * @param options.taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @param options.discount - Optional discount to apply
 * @returns Total price rounded to 2 decimal places
 * @throws {ValidationError} If items array is empty
 *
 * @example
 * ```ts
 * const total = calculateTotal(
 *   [{ price: 10, quantity: 2 }],
 *   { taxRate: 0.08 }
 * );
 * // Returns: 21.60
 * ```
 */
function calculateTotal(items: CartItem[], options: CalculateOptions): number {
  // ...
}

/**
 * User account information.
 */
interface User {
  /** Unique identifier */
  id: string;
  /** User's display name */
  name: string;
  /** Email address (unique) */
  email: string;
  /** Account creation timestamp */
  createdAt: Date;
  /** User role for authorization */
  role: "admin" | "user" | "guest";
}
````

## README Structure

### Essential Sections

```markdown
# Project Name

Brief description of what the project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

\`\`\`bash
npm install package-name
\`\`\`

## Quick Start

\`\`\`typescript
import { something } from 'package-name';

// Basic usage example
const result = something();
\`\`\`

## Usage

### Basic Usage

[More detailed examples]

### Configuration

[Configuration options]

### Advanced Usage

[Advanced examples]

## API Reference

### `functionName(params)`

[API documentation]

## Contributing

[How to contribute]

## License

MIT
```

## API Documentation

### Rules

- ✅ DO: Document all endpoints/functions
- ✅ DO: Include request/response examples
- ✅ DO: Document error responses
- ✅ DO: Keep examples up to date
- ❌ DON'T: Document internal implementation
- ❌ DON'T: Assume reader knows context

### Example

```markdown
## Create User

Creates a new user account.

### Request

\`POST /api/users\`

#### Headers

| Header        | Required | Description      |
| ------------- | -------- | ---------------- |
| Authorization | Yes      | Bearer token     |
| Content-Type  | Yes      | application/json |

#### Body

\`\`\`json
{
"name": "John Doe",
"email": "john@example.com",
"password": "securepassword123"
}
\`\`\`

| Field    | Type   | Required | Description                       |
| -------- | ------ | -------- | --------------------------------- |
| name     | string | Yes      | User's display name (1-100 chars) |
| email    | string | Yes      | Valid email address               |
| password | string | Yes      | Password (min 8 chars)            |

### Response

#### Success (201 Created)

\`\`\`json
{
"id": "user_123",
"name": "John Doe",
"email": "john@example.com",
"createdAt": "2024-01-15T10:30:00Z"
}
\`\`\`

#### Errors

| Status | Code             | Description              |
| ------ | ---------------- | ------------------------ |
| 400    | VALIDATION_ERROR | Invalid input            |
| 409    | EMAIL_EXISTS     | Email already registered |
| 500    | SERVER_ERROR     | Internal error           |
```

## Inline Comments

### Rules

- ✅ DO: Explain "why", not "what"
- ✅ DO: Document workarounds and edge cases
- ✅ DO: Add TODO/FIXME with ticket numbers
- ❌ DON'T: Comment obvious code
- ❌ DON'T: Leave outdated comments

### Examples

```typescript
// ❌ Bad - obvious
// Loop through users
for (const user of users) {
  // Check if user is active
  if (user.isActive) {
    // Add to list
    activeUsers.push(user);
  }
}

// ✅ Good - explains why
// Filter out users who haven't verified email - required for GDPR compliance
const verifiedUsers = users.filter((u) => u.emailVerified);

// ✅ Good - documents workaround
// HACK: Safari doesn't support date input, fallback to text
// See: https://bugs.webkit.org/show_bug.cgi?id=12345
const inputType = isSafari ? "text" : "date";

// ✅ Good - TODO with context
// TODO(AUTH-456): Add rate limiting before production launch
// Currently no protection against brute force attacks
async function login(credentials: Credentials) {
  // ...
}

// ✅ Good - explains complex logic
// Using binary search because users array is sorted by ID
// and we need O(log n) lookup for real-time features
const userIndex = binarySearch(users, targetId);
```

## Changelog

### Format (Keep a Changelog)

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added

- New feature X

## [1.2.0] - 2024-01-15

### Added

- User authentication with OAuth
- Password reset functionality

### Changed

- Improved error messages for validation

### Fixed

- Fix memory leak in WebSocket connection

### Deprecated

- `oldFunction()` - use `newFunction()` instead

### Removed

- Removed support for Node.js 14

### Security

- Updated dependencies to fix CVE-2024-1234
```

## Architecture Decision Records (ADR)

### Template

```markdown
# ADR-001: Use PostgreSQL for primary database

## Status

Accepted

## Context

We need to choose a primary database for the application.
Requirements: ACID compliance, JSON support, scalability.

## Decision

We will use PostgreSQL 15.

## Consequences

### Positive

- Strong ACID compliance
- Excellent JSON support with JSONB
- Large ecosystem and community

### Negative

- More complex setup than SQLite
- Requires managed service or maintenance

### Neutral

- Team needs PostgreSQL training
```

## Documentation Tools

| Tool            | Use Case                |
| --------------- | ----------------------- |
| TypeDoc         | TypeScript API docs     |
| Docusaurus      | Documentation websites  |
| Storybook       | Component documentation |
| Swagger/OpenAPI | REST API documentation  |
| Mermaid         | Diagrams in Markdown    |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
