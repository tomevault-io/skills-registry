---
name: sentry-agents-md
description: Generate and maintain AGENTS.md files with concise agent instructions for AI coding assistants. Use when setting up project-level AI configuration. Use when this capability is needed.
metadata:
  author: allanninal
---

# AGENTS.md Generation

## When to Use This Skill

- Setting up a new project for AI assistance
- Documenting project conventions for AI
- Creating context for Claude Code or other agents
- Standardizing AI behavior across team

## What is AGENTS.md?

AGENTS.md is a project-level configuration file that provides context and instructions to AI coding assistants. It helps AI understand:

- Project structure and conventions
- Coding standards and patterns
- Domain-specific knowledge
- Things to avoid

## File Structure

```markdown
# AGENTS.md

## Project Overview
Brief description of what this project does.

## Tech Stack
- Language: TypeScript
- Framework: Next.js 14
- Database: PostgreSQL with Prisma
- Testing: Jest, Playwright

## Directory Structure
```
src/
├── app/          # Next.js app router pages
├── components/   # React components
├── lib/          # Shared utilities
├── server/       # Server-side code
└── types/        # TypeScript types
```

## Conventions

### Code Style
- Use functional components with hooks
- Prefer named exports
- Use TypeScript strict mode

### Naming
- Components: PascalCase (UserProfile.tsx)
- Utilities: camelCase (formatDate.ts)
- Types: PascalCase with prefix (TUser, IConfig)

### Patterns
- Use React Query for data fetching
- Use Zustand for client state
- Use server actions for mutations

## Important Context
[Domain-specific information the AI should know]

## Common Tasks
[Frequent operations and how to do them]

## Avoid
- Don't use `any` type
- Don't use inline styles
- Don't commit .env files
```

## Template Generator

### Basic Template

```markdown
# AGENTS.md

## Project: [Name]
[One-line description]

## Quick Start
```bash
npm install
npm run dev
```

## Tech Stack
- [Language/Runtime]
- [Framework]
- [Database]
- [Key Libraries]

## Project Structure
```
[directory tree]
```

## Coding Conventions
1. [Convention 1]
2. [Convention 2]
3. [Convention 3]

## Testing
- Run tests: `npm test`
- Test pattern: [describe what tests look like]

## Deployment
[How to deploy]

## Key Files
- `[file]`: [purpose]
- `[file]`: [purpose]

## Don't
- [Thing to avoid]
- [Thing to avoid]
```

### Full Template

```markdown
# AGENTS.md

## Overview
[Project name] is a [type of application] that [main purpose].

### Key Features
- [Feature 1]
- [Feature 2]
- [Feature 3]

## Architecture

### Tech Stack
| Layer | Technology |
|-------|------------|
| Frontend | [React/Vue/etc] |
| Backend | [Node/Python/etc] |
| Database | [PostgreSQL/MongoDB/etc] |
| Cache | [Redis/Memcached/etc] |
| Queue | [RabbitMQ/SQS/etc] |

### Directory Structure
```
project/
├── src/
│   ├── api/           # API routes
│   ├── components/    # UI components
│   ├── hooks/         # Custom hooks
│   ├── lib/           # Utilities
│   ├── services/      # Business logic
│   └── types/         # Type definitions
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/
└── scripts/
```

## Development Workflow

### Setup
```bash
git clone [repo]
cd [project]
npm install
cp .env.example .env
npm run db:migrate
npm run dev
```

### Branch Naming
- `feat/[ticket]-description`
- `fix/[ticket]-description`
- `chore/[ticket]-description`

### Commit Format
```
type(scope): subject

body

footer
```

## Coding Standards

### TypeScript
```typescript
// Prefer interfaces for objects
interface User {
  id: string;
  name: string;
  email: string;
}

// Use type for unions/primitives
type Status = 'active' | 'inactive';

// Always type function returns
function getUser(id: string): Promise<User> {
  // ...
}
```

### React Components
```tsx
// Functional components with named exports
export function UserCard({ user }: { user: User }) {
  return (
    <div className="user-card">
      <h2>{user.name}</h2>
    </div>
  );
}
```

### Error Handling
```typescript
// Always handle errors explicitly
try {
  await riskyOperation();
} catch (error) {
  if (error instanceof SpecificError) {
    // Handle specific case
  }
  throw error;
}
```

## Testing

### Unit Tests
```typescript
describe('UserService', () => {
  it('should create a user', async () => {
    const user = await userService.create({ name: 'Test' });
    expect(user.name).toBe('Test');
  });
});
```

### Integration Tests
```typescript
describe('POST /api/users', () => {
  it('should create user and return 201', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Test', email: 'test@example.com' });
    expect(response.status).toBe(201);
  });
});
```

## API Conventions

### REST Endpoints
```
GET    /api/resources          # List
GET    /api/resources/:id      # Get one
POST   /api/resources          # Create
PUT    /api/resources/:id      # Update
DELETE /api/resources/:id      # Delete
```

### Response Format
```json
{
  "data": {},
  "meta": {
    "page": 1,
    "total": 100
  },
  "errors": []
}
```

## Database

### Migrations
```bash
npm run db:migrate      # Run migrations
npm run db:rollback     # Rollback last
npm run db:seed         # Seed data
```

### Naming Conventions
- Tables: plural, snake_case (`user_profiles`)
- Columns: snake_case (`created_at`)
- Indexes: `idx_table_column`

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| DATABASE_URL | Database connection | Yes |
| API_KEY | External API key | Yes |
| DEBUG | Enable debug mode | No |

## Common Gotchas

1. **[Gotcha 1]**: [Explanation and solution]
2. **[Gotcha 2]**: [Explanation and solution]

## Resources

- [Documentation](link)
- [Design System](link)
- [API Reference](link)

## Contact

- Team: [team-name]
- Slack: #[channel]
```

## Best Practices

- [ ] Keep AGENTS.md concise but complete
- [ ] Update when project evolves
- [ ] Include examples, not just rules
- [ ] Document gotchas and edge cases
- [ ] Link to detailed docs for complex topics
- [ ] Include quick start commands
- [ ] List things to avoid explicitly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
