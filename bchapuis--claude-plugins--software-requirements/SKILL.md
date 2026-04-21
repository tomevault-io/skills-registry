---
name: software-requirement
description: This skill should be used when the user asks to "write requirements", "define requirements", "create user stories", "formalize use cases", "functional requirements", "non-functional requirements", or discusses what a system should do before designing it. Helps refine informal descriptions into structured requirements. Use when this capability is needed.
metadata:
  author: bchapuis
---

# Requirements Methodology

## Overview

Requirements define *what* a system should do before *how* it should be designed. This methodology refines informal user input into structured, actionable requirements.

## Requirement Types

### Functional Requirements

What the system must *do* - specific behaviors and features:
- Actions the system performs
- Data it processes
- Outputs it produces
- Interactions it supports

Format: "The system shall [verb] [object] [condition/constraint]"

Examples:
- The system shall authenticate users via email and password
- The system shall generate monthly reports in PDF format
- The system shall notify users when their order ships

### Non-Functional Requirements

How the system must *perform* - quality attributes:

| Category | Concerns |
|----------|----------|
| Performance | Response time, throughput, latency |
| Scalability | Users, data volume, growth |
| Reliability | Uptime, fault tolerance, recovery |
| Security | Authentication, authorization, encryption |
| Usability | Accessibility, learnability, efficiency |
| Maintainability | Modularity, testability, documentation |

Format: "The system shall [quality] [measurable criteria]"

Examples:
- The system shall respond to queries within 200ms (p95)
- The system shall support 10,000 concurrent users
- The system shall achieve 99.9% uptime

### Constraints

Limitations imposed on the solution - technical, business, or regulatory:

| Type | Examples |
|------|----------|
| Technical | Must use PostgreSQL, must run on AWS |
| Business | Budget limit, timeline, team size |
| Regulatory | GDPR compliance, HIPAA, PCI-DSS |
| Integration | Must integrate with existing CRM |

Format: "The system must [constraint]"

Examples:
- The system must be implemented in Python
- The system must comply with GDPR
- The system must integrate with Salesforce API

## User Stories

Capture requirements from the user's perspective.

### Format

```
As a [role]
I want [goal/desire]
So that [benefit/value]
```

### Acceptance Criteria

Each story includes testable criteria using Given/When/Then:

```
Given [precondition]
When [action]
Then [expected result]
```

### Example

```
As a customer
I want to track my order status
So that I know when to expect delivery

Acceptance Criteria:
- Given I have placed an order
  When I view my orders
  Then I see the current status (processing/shipped/delivered)

- Given my order has shipped
  When the carrier updates tracking
  Then I receive an email notification
```

### Story Quality (INVEST)

- **I**ndependent - Can be developed separately
- **N**egotiable - Details can be discussed
- **V**aluable - Delivers value to user/business
- **E**stimable - Can estimate effort
- **S**mall - Fits in one iteration
- **T**estable - Clear acceptance criteria

## Use Cases

Describe interactions between actors and the system.

### Format

```
Use Case: [Name]
Actor: [Who initiates]
Preconditions: [What must be true before]
Postconditions: [What is true after]

Main Flow:
1. [Step]
2. [Step]
3. [Step]

Alternative Flows:
- [Variation]: [Steps]

Exception Flows:
- [Error condition]: [Handling]
```

### Example

```
Use Case: Place Order
Actor: Customer
Preconditions: Customer is logged in, cart is not empty
Postconditions: Order is created, inventory reserved, confirmation sent

Main Flow:
1. Customer reviews cart contents
2. Customer enters shipping address
3. Customer selects shipping method
4. Customer enters payment information
5. System validates payment
6. System creates order
7. System sends confirmation email

Alternative Flows:
- Step 2: Customer selects saved address

Exception Flows:
- Step 5: Payment declined → Show error, return to step 4
- Step 6: Item out of stock → Notify customer, offer alternatives
```

## Refinement Process

To refine informal input into structured requirements:

1. **Extract functional requirements** - What actions/behaviors are described?
2. **Identify non-functional requirements** - What quality attributes matter?
3. **Note constraints** - What limitations exist?
4. **Write user stories** - Who benefits and how?
5. **Define use cases** - What are the key interactions?
6. **Add acceptance criteria** - How do we verify each requirement?

## Output Structure

```markdown
# Requirements: [System Name]

## Functional Requirements
- FR1: [requirement]
- FR2: [requirement]

## Non-Functional Requirements
- NFR1: [requirement]
- NFR2: [requirement]

## Constraints
- C1: [constraint]
- C2: [constraint]

## User Stories

### Story 1: [Title]
As a [role]...

Acceptance Criteria:
- Given... When... Then...

## Use Cases

### UC1: [Name]
Actor: ...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
