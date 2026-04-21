---
name: functional-prototype
description: Build working prototypes from PRDs with mock data and stubbed integrations. Use when user has a PRD, spec, or feature description and wants a clickable demo to validate flows before full implementation. Triggers: build a prototype, functional prototype, make a demo, implement this PRD, prototype this spec, make it clickable. Use when this capability is needed.
metadata:
  author: porkbutts
---

# Functional Prototype Builder

Build working prototypes from PRDs with realistic mock data and stubbed dependencies.

## Workflow

### 1. Analyze PRD

Extract:
- **User stories**: Each user-facing flow to demonstrate
- **Screens**: All unique views needed
- **Data entities**: Users, posts, products, etc.
- **External dependencies**: Database, auth, payment, APIs
- **Tech stack**: Ask user if not specified

### 2. Create Mock Layer

Create a dedicated mocks directory with:
- **Mock data file**: Precanned realistic data (5-10 items per entity, realistic names/content, edge cases like empty states)
- **Stub API file**: Functions that return mock data with simulated delays (100-300ms)

**Stub patterns by dependency type:**

| Dependency | Stub Pattern |
|------------|--------------|
| Database | Return filtered/sorted mock data |
| Auth | Return mock user, always succeed |
| File upload | Store in memory/localStorage |
| Payment | Return success with fake transaction ID |
| External API | Return realistic mock response |
| Email/SMS | Console.log the message |
| Search | Filter mock data client-side |

### 3. Build Screens

For each screen:
- Import from stub layer (never real dependencies)
- Implement full UI with interactions
- Handle loading states (stubs have delays)
- Handle empty/error states
- Complete navigation between screens

### 4. Generate Integration Guide (Optional)

After prototype is complete, ask user: *"Would you like me to generate an integration guide documenting how to replace stubs with real implementations?"*

If yes, create `docs/integration-guide.md` following [references/integration-guide-template.md](references/integration-guide-template.md).

## Output Checklist

- [ ] All user stories from PRD are demonstrable
- [ ] Every screen is implemented and navigable
- [ ] Mock data looks realistic
- [ ] Stubs simulate async behavior (delays)
- [ ] Loading and empty states handled

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
