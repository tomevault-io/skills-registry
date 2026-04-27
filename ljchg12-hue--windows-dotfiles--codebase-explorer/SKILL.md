---
name: codebase-explorer
description: Expert codebase exploration including architecture analysis, pattern discovery, and navigation assistance Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Codebase Explorer

## Purpose
Explore and understand codebases through systematic analysis, architecture mapping, pattern discovery, and intelligent navigation.

## Activation Keywords
- explore codebase, understand code
- architecture analysis, code structure
- how does this work, where is
- codebase map, code navigation
- find pattern, code flow

## Core Capabilities

### 1. Structure Analysis
- Directory organization
- Module dependencies
- Layer identification
- Entry points
- Configuration locations

### 2. Architecture Mapping
- High-level architecture
- Component relationships
- Data flow paths
- Service boundaries
- Integration points

### 3. Pattern Discovery
- Design patterns used
- Coding conventions
- Common utilities
- Error handling patterns
- Testing patterns

### 4. Flow Tracing
- Request/response flow
- Data transformation
- Event propagation
- Error paths
- Authentication flow

### 5. Navigation
- Key file identification
- Related file discovery
- Dependency tracking
- Impact analysis
- Quick reference creation

## Exploration Process

```
1. Initial Survey
   → Package.json/requirements.txt
   → Directory structure
   → README and docs
   → Entry points (main, index)

2. Architecture Understanding
   → Identify layers (UI, API, Data)
   → Map major components
   → Trace dependencies
   → Find configuration

3. Deep Dive
   → Core business logic
   → Data models
   → API endpoints
   → Integration points

4. Pattern Recognition
   → Design patterns
   → Coding conventions
   → Utility functions
   → Error handling

5. Documentation
   → Architecture diagram
   → Key file reference
   → Flow documentation
   → Gotchas and notes
```

## Codebase Map Template

```markdown
## Codebase Map: [Project Name]

### Overview
- **Type**: [Web app/API/Library/CLI]
- **Stack**: [Languages and frameworks]
- **Size**: [Files/Lines estimate]

### Directory Structure
```
project/
├── src/
│   ├── components/    # UI components
│   ├── services/      # Business logic
│   ├── models/        # Data models
│   └── utils/         # Utilities
├── tests/
├── config/
└── docs/
```

### Key Files
| Purpose | Location |
|---------|----------|
| Entry point | src/index.ts |
| Config | config/settings.ts |
| Routes | src/routes/index.ts |
| Database | src/db/connection.ts |

### Architecture Layers
```
[Client] → [API Layer] → [Service Layer] → [Data Layer]
              │              │                │
              └── Routes     └── Business     └── DB/Cache
                             Logic
```

### Key Patterns
- [Pattern 1]: Used for [purpose]
- [Pattern 2]: Used for [purpose]

### Data Flow
1. Request → Router → Controller
2. Controller → Service → Repository
3. Repository → Database
4. Response ← Controller ← Service

### Important Conventions
- [Convention 1]
- [Convention 2]
```

## Architecture Patterns to Look For

| Pattern | Indicators |
|---------|------------|
| MVC | controllers/, models/, views/ |
| Clean Architecture | domain/, application/, infrastructure/ |
| Microservices | services/, api-gateway/ |
| Monorepo | packages/, apps/, libs/ |
| Feature-based | features/, modules/ |
| DDD | domain/, aggregates/, entities/ |

## Example Usage

```
User: "Explore this Next.js project structure"

Codebase Explorer Response:

## Codebase Map: Next.js Application

### Overview
- Type: Full-stack web application
- Stack: Next.js 14, TypeScript, Prisma, Tailwind
- Size: ~150 files, ~15k lines

### Directory Structure
```
app/
├── (auth)/           # Auth route group
├── (dashboard)/      # Dashboard routes
├── api/              # API routes
└── layout.tsx        # Root layout

components/
├── ui/               # Base UI (shadcn)
└── features/         # Feature components

lib/
├── db.ts            # Prisma client
├── auth.ts          # Auth utilities
└── utils.ts         # General utils

prisma/
└── schema.prisma    # Database schema
```

### Key Files
| Purpose | Location |
|---------|----------|
| Root layout | app/layout.tsx |
| Auth config | lib/auth.ts |
| DB client | lib/db.ts |
| API routes | app/api/[...]/route.ts |

### Architecture
- App Router (Next.js 14)
- Route groups for organization
- Server Components by default
- Prisma for database
- NextAuth for authentication

### Patterns Found
- Server Actions for mutations
- React Query for client state
- Shadcn/ui component library
- Zod for validation

### Entry Flow
1. layout.tsx (providers)
2. page.tsx (route component)
3. Server Component → Data fetch
4. Client Component → Interactivity
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
