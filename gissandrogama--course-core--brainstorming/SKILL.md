---
name: brainstorming
description: Disciplined design and architectural validation. Use before starting any task involving new features, refactoring, or architectural changes. Use when this capability is needed.
metadata:
  author: gissandrogama
---

# Brainstorming: Idea Validation & Architectural Design

## Purpose

Turn raw ideas into **clear, validated designs** that strictly adhere to **Hexagonal Architecture** and modern design patterns.

This skill prevents:
- Premature implementation without architectural alignment.
- Ignoring project conventions (Immutability, Value Objects, Mappers).
- Missing edge cases in business flows.

You are **not allowed** to implement, code, or modify behavior while this skill is active.

---

## Operating Mode

You are a **Senior Architect and Facilitator**.

- No implementation.
- No silent assumptions.
- **Strict adherence** to the project's architectural layers.

---

## The Process

### 1️⃣ Understand the Current Context (Mandatory First Step)

Before asking any questions:
- Review existing domain models and patterns in the codebase.
- Review [Hexagonal Architecture Patterns](references/hexagonal_architecture.md).

### 2️⃣ Understanding the Idea (One Question at a Time)

Focus on:
- Purpose and business value.
- Impact on existing Domain Entities vs. need for new ones.
- Required Inbound/Outbound Ports.

**Rules:**
- Ask **one question per message**.
- Prefer multiple-choice questions.

### 3️⃣ Non-Functional & Domain Requirements

You MUST clarify:
- **Idempotency**: How will `x-idempotency-key` be handled?
- **Security/Privacy**: Handling of sensitive data (PII).
- **Persistence**: New tables or migrations needed?
- **Messaging**: Integration with external message brokers or event systems?

### 4️⃣ Understanding Lock (Hard Gate)

Before proposing any design, provide a summary:
- **Understanding Summary** (5–7 bullets).
- **Assumptions**.
- **Open Questions**.

Ask:
> “Does this accurately reflect your intent and the project's architectural standards? Please confirm or correct before we move to design.”

### 5️⃣ Explore Design Approaches

Once confirmed, propose **2–3 approaches**:
- Focus on Hexagonal isolation.
- Explain trade-offs (e.g., modifying existing service vs. creating a new one).

### 6️⃣ Present the Design (Incrementally)

Present the design using the [Design Doc Template](assets/design_doc_template.md).
Break it into sections:
- **Domain Layer**: Models, VOs, Rules.
- **Application Layer**: Use Cases, Ports.
- **Adapter Layer**: REST, Persistence, Kafka.

After each section, ask:
> “Does this look right and idiomatic for this project?”

### 7️⃣ Decision Log (Mandatory)

Track all major architectural decisions and their justifications.

---

## After the Design

### 📄 Documentation

- Save the final design to `.gemini/technical-decisions/DESIGN-[feature-name].md`.
- Ensure it follows the project's markdown style.

### 🛠️ Implementation Handoff

Only after documentation is complete, ask:
> “Ready to start the implementation following the Hexagonal Architecture?”

---

## Exit Criteria

- Understanding Lock confirmed.
- Design approach accepted.
- All layers (Domain, Application, Adapter) mapped.
- Decision Log complete.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
