---
name: wow-resume
description: Analyze codebase and Git commit history to discover quantifiable technical achievements for resume writing. Use when user asks for resume preparation, portfolio organization, or project highlight extraction. Use when this capability is needed.
metadata:
  author: fingersnap-dev
---

# Wow Resume

## Overview
Deep-dive into the entire codebase and Git commit history to discover technical achievements worth highlighting on a resume. Instead of listing features, organize quantified problem-solving experiences in STAR format.

## Core Principles
- **Commits reveal the journey**: Commit history shows problem recognition and improvement process—the story recruiters want to hear
- **No metrics, no appeal**: Non-quantifiable achievements are deprioritized
- **Filter out the mundane**: Exclude basics like "Built REST API with Spring Boot"
- **Stack-specific focus**: Select only points with technical depth relevant to the domain

## Workflow

### Step 0: Determine Preferred Language
Identify the user's preferred language for the output document:
- Explicit prompt instruction (e.g., "write in Korean")
- Guide files like `AGENTS.md`, `CLAUDE.md`, `.cursorrules`
- Language of the user's question

Write all output documents in the user's preferred language.

### Step 1: Detect Project Stack
Automatically identify the project's technology stack.

**Files to analyze:**
- `package.json`, `build.gradle`, `pom.xml`, `requirements.txt`, `Podfile`, `Cargo.toml`, etc.

**Domain classification (examples):**
- Backend (Java/Spring, Node.js, Python/Django, Go, etc.)
- Frontend (React, Vue, Angular, Next.js, etc.)
- Mobile (iOS/Swift, Android/Kotlin, Flutter, React Native, etc.)
- Game (Unity, Unreal, etc.)
- Infra/DevOps (Kubernetes, Terraform, Docker, etc.)
- Data/ML, Embedded, Blockchain, etc.

If the project belongs to a different domain, identify and apply domain-specific highlight criteria accordingly.

### Step 2: Exhaustive Codebase Analysis
Read the entire codebase line by line. Do not skim—allocate maximum effort to understand every file, every function, every implementation detail. This step focuses on **the current state** of the code.

**Read everything:**
- Every source file, configuration, and script
- All modules, classes, functions, and their implementations
- Comments, TODOs, and inline documentation
- Test files and what they cover

**While reading, identify:**
- Directory structure and module boundaries
- Architecture patterns (Layered, Hexagonal, Clean Architecture, MVC, etc.)
- Complex algorithms and non-trivial logic
- Performance-sensitive code paths
- External integrations and how they're handled
- Test coverage and testing strategies
- CI/CD pipeline configuration

**Output:**
Complete understanding of the codebase with identification of technically impressive areas

### Step 3: Deep Git History Analysis
Read the entire commit history thoroughly. This step focuses on **the changes and evolution**—how problems were recognized and solved over time.

**3-1. Collect commit logs**
```bash
git log --oneline --all --pretty=format:"%h %s" 
git log --stat
```

**3-2. Filter meaningful commits**
High-priority commit types:
- `fix:` - Bug fixes, problem resolution
- `perf:` - Performance improvements
- `refactor:` - Code structure improvements
- Large-scale changes (by lines changed, files affected)

Exclude:
- Simple typo fixes
- Formatting/lint-only commits
- Initial setup, boilerplate

**3-3. Analyze diff per commit**
```bash
git show <commit-hash>
git diff <before>..<after>
```

Extract from each commit:
- Before: What problem/state existed previously
- After: How it was changed
- Why: Reason for the change (from commit message, PR description)

### Step 4: Match Domain-Specific Highlight Points
Apply checklists based on stack identified in Step 1.

**Important:** The checklists below are **examples only**. Do not limit your search to these items. Actively discover and include **any technically impressive point** found in the codebase—if it demonstrates problem-solving, optimization, or engineering depth, it's worth highlighting.

#### Backend Checklist (Examples)
| Category | Highlight Point | Quantification Example |
|----------|-----------------|----------------------|
| DB Optimization | Index design, query tuning, execution plan analysis | Query time 500ms → 20ms |
| Concurrency Control | Transaction isolation level, locking strategy, deadlock resolution | 0% conflicts at 1000 concurrent requests |
| ORM Optimization | N+1 resolution, bulk operations, fetch join | Query count 150 → 3 |
| Async Processing | Event-driven, message queue, non-blocking | Throughput 500 req/s → 2000 req/s |
| Caching | Cache strategy, invalidation policy, multi-layer cache | 95% cache hit rate, 80% response time reduction |
| API Design | Pagination, rate limiting, bulk API | Memory usage 2GB → 200MB |

#### Frontend Checklist (Examples)
| Category | Highlight Point | Quantification Example |
|----------|-----------------|----------------------|
| Rendering Optimization | Virtualization, memoization, re-render prevention | 60 FPS maintained with 10K list items |
| Bundle Optimization | Code splitting, tree shaking, lazy loading | Bundle size 2MB → 400KB |
| State Management | Normalization, selective subscription, immutability | 90% reduction in unnecessary re-renders |
| Core Web Vitals | LCP, FID, CLS improvements | LCP 4s → 1.2s |
| Memory Management | Memory leak fixes, cleanup patterns | Memory usage 500MB → 100MB |

#### Mobile Checklist (Examples)
| Category | Highlight Point | Quantification Example |
|----------|-----------------|----------------------|
| UI Performance | Main thread blocking prevention, layout optimization | 0 frame drops during scroll |
| Memory | Image caching, memory leak fixes | Peak memory 300MB → 80MB |
| Network | Offline support, request optimization, sync | 50% data usage reduction |
| Battery | Background task optimization, GPS/sensor management | 30% battery consumption reduction |
| App Size | Resource optimization, dynamic delivery | APK size 100MB → 40MB |

#### Game Checklist (Examples)
| Category | Highlight Point | Quantification Example |
|----------|-----------------|----------------------|
| Rendering | Draw call batching, LOD, occlusion culling | Draw calls 3000 → 200 |
| Physics/AI | Spatial partitioning, object pooling | 60 FPS with 10K simultaneous objects |
| Memory | Asset streaming, GC minimization | GC spike 50ms → 2ms |
| Network | Client prediction, interpolation, rollback netcode | Smooth gameplay at 150ms latency |

**Beyond the checklists:**
Look for any achievement that shows technical depth, including but not limited to:
- Clever algorithm implementations
- Security hardening measures
- Accessibility improvements with measurable impact
- Developer experience improvements (CI/CD, tooling, documentation)
- Cost optimization (cloud resources, API calls, etc.)
- Reliability improvements (error handling, retry logic, graceful degradation)
- Data integrity measures (validation, constraints, migration strategies)

**Mundane filtering:**
Exclude the following from highlight points:
- Basic CRUD implementation
- Default framework configuration
- Tutorial-level features
- Simple library integration

### Step 5: Discover Future Improvement Opportunities

This step is equally important as finding existing highlights. Identify improvements that are **not yet applied** but would become strong resume points if implemented. This helps build a roadmap for an impressive resume.

#### 5-1. Quick Wins (Immediately Applicable)

Improvements that can be applied to the current codebase without major assumptions.

**Analysis method:**
1. Compare Step 4 checklists with current codebase
2. Identify gaps where best practices are not yet applied
3. Evaluate implementation difficulty vs. resume impact

**Examples:**

| Current State | Recommended Improvement | Expected Highlight |
|---------------|------------------------|-------------------|
| Many simple queries | Index design + query optimization | "N% query performance improvement" |
| No caching layer | Local/Redis caching introduction | "N% API response time reduction" |
| Synchronous processing | Async/event-driven conversion | "Nx throughput increase" |
| Full data loading | Cursor-based pagination | "Memory usage reduced by N%" |
| No rate limiting | Rate limiter implementation | "System stability under load" |

#### 5-2. Scenario-Based Improvements (Assuming Scale)

Go beyond current state. **Assume realistic production scenarios** and identify what problems would arise and how to solve them. This demonstrates forward-thinking engineering mindset.

**Important:** The scenarios below are **examples only**. Do not limit yourself to these patterns. Actively imagine any realistic scenario relevant to the project—whether scale-related, feature evolution, or domain-specific challenges. The more improvement opportunities you discover, the better.

**How to approach:**
1. Define a realistic scale scenario for the project type
2. Identify bottlenecks and failure points at that scale
3. Propose solutions with expected outcomes

**Backend scenario examples (Scale):**

| Assumed Scenario | Expected Problem | Proposed Solution | Resume Highlight |
|------------------|------------------|-------------------|------------------|
| 100 TPS sustained | DB connection exhaustion | Connection pooling (HikariCP tuning), read replicas | "Designed for 100 TPS with connection pool optimization" |
| 1000 concurrent users | Session storage bottleneck | Redis session clustering, sticky sessions | "Horizontal scaling with distributed session management" |
| 10K requests/min spike | Server overload, cascading failure | Circuit breaker, bulkhead pattern, graceful degradation | "Implemented resilience patterns for 10K req/min spikes" |
| 1M records in table | Slow queries, full table scans | Partitioning, covering indexes, query optimization | "Optimized for 1M+ records with partitioning strategy" |
| Multi-region deployment | Data consistency, latency | CQRS, eventual consistency, CDC | "Designed multi-region architecture with eventual consistency" |
| Concurrent inventory updates | Race condition, overselling | Optimistic/pessimistic locking, distributed lock (Redisson) | "Zero overselling with distributed lock under 1000 concurrent orders" |
| Concurrent payment processing | Double payment, lost updates | Idempotency key, transaction isolation, saga pattern | "Idempotent payment processing with 100% consistency" |
| Shared resource contention | Deadlock, performance degradation | Lock ordering, timeout-based retry, lock-free algorithms | "Eliminated deadlocks with lock ordering strategy" |

**Backend scenario examples (Feature Evolution):**

| Current State | Feature Addition | Expected Challenge | Proposed Solution | Resume Highlight |
|---------------|------------------|-------------------|-------------------|------------------|
| Single-user editing | Real-time collaborative editing | Conflict resolution, operational ordering | OT/CRDT algorithms, WebSocket sync | "Implemented CRDT-based real-time collaboration for N concurrent editors" |
| Single tenant | Multi-tenancy support | Data isolation, schema management | Tenant-aware middleware, row-level security | "Designed multi-tenant architecture with complete data isolation" |
| Synchronous API | Webhook/event notification | Delivery guarantee, retry logic | Outbox pattern, dead letter queue | "Reliable webhook delivery with 99.9% success rate" |
| Basic text content | Rich media support (images, video) | Storage, processing, CDN | Object storage, async transcoding, edge caching | "Scalable media pipeline processing N GB/day" |
| Manual workflow | Automated pipeline/scheduling | State management, failure recovery | State machine, idempotent jobs, checkpoint/restart | "Zero-downtime automated pipeline with failure recovery" |
| REST API only | Real-time updates | Connection management, scaling | WebSocket with Redis pub/sub, connection pooling | "Real-time sync for N concurrent connections" |
| Monolith | Service extraction | Transaction boundaries, data consistency | Saga pattern, event sourcing, API gateway | "Extracted N microservices with distributed transaction handling" |

**Frontend scenario examples (Scale):**

| Assumed Scenario | Expected Problem | Proposed Solution | Resume Highlight |
|------------------|------------------|-------------------|------------------|
| 10K list items | Scroll jank, memory bloat | Virtual scrolling, windowing | "Smooth 60 FPS with 10K+ items via virtualization" |
| 3G network users | Slow initial load, timeouts | Aggressive code splitting, service worker, offline-first | "Optimized for 3G: 2s LCP with offline support" |
| 100+ components | Bundle size explosion | Dynamic imports, route-based splitting | "Reduced initial bundle by N% with lazy loading" |

**Frontend scenario examples (Feature Evolution):**

| Current State | Feature Addition | Expected Challenge | Proposed Solution | Resume Highlight |
|---------------|------------------|-------------------|-------------------|------------------|
| Static content | Real-time collaboration | State sync, cursor presence | Yjs/Automerge, presence API, WebSocket | "Implemented Google Docs-like collaboration with N ms sync latency" |
| Page-based navigation | Infinite scroll / virtualization | Memory management, scroll position | Intersection Observer, virtual list | "Infinite scroll with constant memory footprint" |
| Form-based input | Drag-and-drop interface | Complex state, undo/redo | Command pattern, immutable state | "Intuitive drag-drop builder with full undo/redo support" |
| Single language | i18n / multi-language | Bundle size, dynamic loading | Lazy-loaded translations, ICU format | "Supported N languages with on-demand translation loading" |

**Mobile scenario examples (Scale):**

| Assumed Scenario | Expected Problem | Proposed Solution | Resume Highlight |
|------------------|------------------|-------------------|------------------|
| Low-end devices (2GB RAM) | OOM crashes, ANR | Memory-efficient image loading, process management | "Optimized for 2GB RAM devices: 0 OOM crashes" |
| Offline-heavy usage | Data sync conflicts | Conflict resolution strategy, local-first architecture | "Robust offline sync with automatic conflict resolution" |
| Background sync every 15min | Battery drain complaints | WorkManager optimization, batching | "Reduced battery usage by N% with smart sync batching" |

**Mobile scenario examples (Feature Evolution):**

| Current State | Feature Addition | Expected Challenge | Proposed Solution | Resume Highlight |
|---------------|------------------|-------------------|-------------------|------------------|
| Pull-to-refresh | Real-time push updates | Battery, connection management | FCM/APNs with smart batching, delta sync | "Real-time updates with minimal battery impact" |
| Online-only | Offline-first with sync | Conflict resolution, queue management | Local DB + sync queue, CRDT | "Full offline capability with seamless sync" |
| Single device | Cross-device sync | State consistency, handoff | Cloud sync, device linking, conflict merge | "Seamless cross-device experience with instant sync" |

**Game scenario examples:**

| Assumed Scenario | Expected Problem | Proposed Solution | Resume Highlight |
|------------------|------------------|-------------------|------------------|
| 1000 active entities | Frame drops, physics lag | Spatial hashing, LOD, entity pooling | "Stable 60 FPS with 1000+ active entities" |
| 200ms average latency | Rubber-banding, desync | Client-side prediction, server reconciliation | "Smooth netplay at 200ms latency with prediction" |

#### 5-3. Recommendation Quality Criteria

**Include only if:**
- Demonstrates technical depth (not just "add library X")
- Appropriate for project scale (don't propose Kubernetes for a todo app)
- Has clear, quantifiable expected outcome
- You can explain the "why" and trade-offs

**Exclude:**
- Over-engineering that doesn't fit context
- Trendy tech without clear benefit
- Improvements without measurable impact

### Step 6: Quantification and Story Composition

**6-1. Extract/Estimate metrics**

Directly extractable from code:
- Loop iteration counts, query count changes
- Configuration changes (timeout, batch size, etc.)
- Test case counts

Inferable from Before/After diff:
- Algorithm complexity changes (O(n²) → O(n))
- Call structure changes

When exact metrics cannot be confirmed, estimate based on code analysis and mark with "(estimated - verify with actual measurement)".

**6-2. STAR format composition**

Organize each highlight point as:
- **Situation**: What situation/problem existed
- **Task**: What needed to be solved
- **Action**: What technical approach was taken
- **Result**: What was the outcome (metrics required!)

### Step 7: Generate Final Output

Save analysis results as **`resume.md` file in project root**.

This document serves as **preparation material**, not the final resume text. Write in detail so you can later condense it for actual resume submission.

**Important:** Do NOT limit the number of highlights or improvement opportunities. Extract as many meaningful points as possible for both existing achievements and future improvements—the user will select and condense later. More material = better choices for the final resume.

**File path:** `./resume.md` (project root)

**Output format:**

````markdown
# [Project Name] Resume Preparation Document

## Project Overview

### Summary
[2-3 paragraphs describing what the project does, its purpose, and your role]

### Tech Stack
| Category | Technologies |
|----------|-------------|
| Language | |
| Framework | |
| Database | |
| Infrastructure | |
| Testing | |

### Scale & Duration
- Development Period: 
- Codebase Size: (files, lines of code)

---

## Key Highlights

### 1. [Highlight Title]

#### Background & Problem Recognition
[Detailed explanation of the context. What was the situation? What symptoms or issues were observed? How did you identify this as a problem worth solving? Include specific observations from code, logs, or user feedback.]

#### Technical Analysis
[Deep dive into the technical aspects. What was the root cause? What alternatives did you consider? Why did you choose this approach over others?]

#### Implementation Details
[Step-by-step explanation of what you actually did. Include:
- Key code changes and why
- Architecture decisions
- Libraries/tools used and why
- Challenges faced during implementation]

**Key Code Snippets:**
```[language]
// Before
[relevant code snippet showing the problem]

// After  
[relevant code snippet showing the solution]
```

#### Results & Metrics
| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| | | | |

[Explain the impact. If metrics are estimated, explain the reasoning.]

#### Lessons Learned
[What did you learn? What would you do differently?]

#### Related Commits
- `[hash]` - [commit message]
- `[hash]` - [commit message]

#### For Resume (Condensed Version)
> [1-2 sentence version suitable for actual resume]

---

### 2. [Highlight Title]
[Same structure as above]

---

## Future Improvement Opportunities

### Quick Wins (Immediately Applicable)

Improvements that can be applied now without major assumptions.

#### 1. [Improvement Title]

##### Current State
[What exists now and its limitations]

##### Proposed Improvement
[Technical approach with rationale]

##### Expected Outcome
| Metric | Before | After (expected) |
|--------|--------|------------------|
| | | |

##### Implementation Notes
- Key changes needed:
- Estimated effort:
- Risk level:

##### For Resume (if implemented)
> [How this would read on a resume]

---

### Scenario-Based Improvements (Assuming Scale)

Improvements designed for realistic production scenarios.

#### 1. [Improvement Title]

##### Assumed Scenario
[Describe the scale/load scenario, e.g., "100 TPS sustained traffic", "1M records", "1000 concurrent users"]

##### Problem Analysis
[What problems would occur at this scale? Why?]

##### Proposed Solution
[Detailed technical approach]

##### Architecture/Design
```
[Diagram or description of the solution architecture]
```

##### Expected Outcome
| Metric | Without Solution | With Solution |
|--------|------------------|---------------|
| | | |

##### Trade-offs & Considerations
[What are the downsides? What alternatives were considered?]

##### Implementation Roadmap
1. [Phase 1]
2. [Phase 2]
3. [Phase 3]

##### For Resume (if implemented)
> [How this would read on a resume—emphasize the scale handled]

---
````

**Post-save message (in user's preferred language):**
Inform the user that:
1. The analysis results have been saved to `resume.md`
2. This is a detailed preparation document—condense the highlights when writing the actual resume
3. Each highlight includes a "For Resume (Condensed Version)" section they can directly use
4. The "Future Improvement Opportunities" section provides a roadmap for strengthening the resume

## Heuristics

- Commit messages with "performance", "optimize", "improve", "fix" keywords → Analyze first
- `fix:` commits with many line changes → Likely complex bug resolution
- `refactor:` commits → Shows code quality awareness
- Commits with test code → Shows quality management awareness
- Commits with PR/issue numbers → Additional context available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fingersnap-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
