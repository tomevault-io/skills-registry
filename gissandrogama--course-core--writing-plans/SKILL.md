---
name: writing-plans
description: Creates detailed, multi-step execution plans for implementation tasks in the open-finance-service. Use after a design is approved via brainstorming and before touching any code.
metadata:
  author: gissandrogama
---

# Writing Execution Plans

## Overview

Write comprehensive implementation plans for the `open-finance-service` (Kotlin/Spring Boot). Plans must be broken down into bite-sized, TDD-driven tasks. Assume the executor knows the project structure but requires explicit, step-by-step guidance for each layer of the **Hexagonal Architecture**.

**Announce at start:** "I'm using the `writing-plans` skill to create the implementation plan."

**Save plans to:** `.gemini/plan/YYYY-MM-DD-<feature-name>.md`

---

## Bite-Sized Task Granularity

Each task must follow the TDD cycle (Red -> Green -> Refactor) and Hexagonal isolation:
1. **Red**: Write a failing test for the specific layer (Domain, Application, or Adapter).
2. **Verify Fail**: Run the test and ensure it fails for the expected reason.
3. **Green**: Implement the minimal code (Service, Entity, or Mapper).
4. **Verify Pass**: Run the test and ensure it passes.
5. **Commit**: Use `semantic-git-commit` skill or standard conventional commits.

---

## Plan Document Header

Utilize a skill `concise-planning` para garantir que a estrutura do plano siga o padrão de **Approach**, **Scope**, **Action Items** e **Validation**.

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing the business value]

**Architecture:** [Mention touched layers: Domain, Ports, Services, Adapters]

**Tech Stack:** Kotlin 2.x, Spring Boot 3.x, JPA/Postgres, Kafka.

---
```

---

## Task Structure Example

### Task N: [Component Name]

**Files:**
- Create: `src/main/kotlin/.../domain/model/NewEntity.kt`
- Modify: `src/main/kotlin/.../application/service/SomeService.kt`
- Test: `src/test/kotlin/.../domain/model/NewEntityTest.kt`

**Step 1: Write the failing test**
Include the code block for the test. Use `mockk` and project builders.

**Step 2: Run test to verify it fails**
Run: `./mvnw test -Dtest=NewEntityTest`
Expected: FAIL (Compilation error or Assertion failure)

**Step 3: Write minimal implementation**
Include the code block for the implementation.

**Step 4: Run test to verify it passes**
Run: `./mvnw test -Dtest=NewEntityTest`
Expected: PASS

**Step 5: Commit**
`git add ... && git commit -m "feat: [sc-XXXXX] add component logic"`

---

## Important Rules

- **Hexagonal Isolation**: Never mix DTOs, Entities, and Domain Models in the same task.
- **Builders First**: If a new Domain Model is created, Task 1 must be creating its Test Builder.
- **Idempotency**: Include tasks for `x-idempotency-key` handling if the operation is a write.
- **Documentation**: Include tasks for updating Swagger/OpenAPI via `swagger-docs-br` skill.
- **Reference Skills**: Mention when to use `@jvm-spring-testing` or `@semantic-git-commit`.

---

## After the Plan

Once the plan is saved, ask the user:

> "Plan complete and saved to `.gemini/plan/<filename>.md`. Ready to start execution step-by-step?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
