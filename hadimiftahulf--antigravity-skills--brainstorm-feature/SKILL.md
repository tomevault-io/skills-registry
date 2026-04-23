---
name: brainstorm-feature
description: Workflow for deeply researching and architecting a new module/feature. STRICTLY NO CODE EXAMPLES. Use this for high-level technical design and research before implementation. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---

# Brainstorm & Research Feature Workflow (Deep Dive)

This skill guides you to produce a **Production-Ready Technical Design** for ANY technology stack (Web, Mobile, Backend, IoT).

> **CRITICAL RULE:** Do NOT include code snippets. Use Architecture Diagrams (Mermaid), Schemas, and Abstract Algorithms.

## Workflow Steps

### 1. Requirement Analysis (The "What")
- **Context Detection**: Identify the project type (e.g., SaaS, E-commerce, Internal Tool) and stack (SQL/NoSQL, Monolith/Microservices).
- **Actors & Roles**: Define exact actors (e.g., Tenant Admin, System, End User, API Client).
- **Invariants**: Define hard constraints (e.g., "Non-negative integers", "Immutable audit logs", "Idempotency keys required").

### 2. Global Benchmarking & Intelligence (Web Research)
- **Search Strategy**: Use `google_web_search` with high-intent enterprise keywords (e.g., "High Availability", "Multi-tenancy isolation", "Audit Trail standards", "Data Sovereignty", "Disaster Recovery patterns").
- **Exclusion Filter**: **STRICTLY AVOID** entry-level tutorials, "How-to" blog posts for beginners, and hobbyist projects.
- **Target Sources**:
    - **Engineering Blogs**: Netflix, Uber, Airbnb, Stripe, Meta, or Stack specific (e.g., Laravel News, Flutter Engineering).
    - **Architecture Centers**: AWS Architecture Center, Azure Well-Architected Framework, Google Cloud Architecture.
    - **Security & Compliance**: SOC2/ISO 27001/GDPR/HIPAA requirements relevant to the feature.
- **Fetch**: Use `read_url_content` to extract deep architectural decisions, trade-offs, and scaling bottlenecks.
- **Synthesis**: Focus on robustness, security-first design, and operational excellence.

### 3. Architecting the Solution (The "Blueprint")
Produce a **Detailed Design** containing:

#### A. Data Model / Schema
- **Structure**:
    - *Relational*: Table Name, Columns, SQL Types, Indexes, FK Constraints.
    - *NoSQL/Document*: Collection Name, Document Structure, Sharding Keys.
- **Relationships**: Cardinality (1:1, 1:N, M:N) and cascading rules.

#### B. State Management & Logic
- **State Machine**: Transitions and guards described logically (use Mermaid State Diagram if complex).
- **Business Rules**: Plain English description of validation and logic (e.g., "If X is active, Y cannot be deleted").

#### C. Interface / API Surface
- **Definition**:
    - *REST*: Method (GET/POST), Path, Request/Response Body keys.
    - *GraphQL*: Query/Mutation names, Inputs, Types.
    - *Internal*: Service Method Signatures (Abstract).
- **Validation Rules**: Abstract rules (e.g., "required", "email format", "max length").

#### D. Performance & Scalability
- **Measured Outcomes**: Target latency, throughput, or caching hit ratios.
- **Concurrency**: Locking strategies (Optimistic vs Pessimistic), Job Queues, Event sourcing.

### 4. Handoff
- **Output**: Technical Design Brief.
- **Compliance Check**: Ensure **ZERO code blocks** are present.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
