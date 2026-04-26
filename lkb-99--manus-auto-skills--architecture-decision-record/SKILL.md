---
name: architecture-decision-record
description: Use when working with a skill for creating and managing Architecture Decision Records (ADRs). Use this skill when users want to document significant architectural choices, create, manage, or validate ADRs. Triggers: ADR, architecture decision record, architectural decision, design document, technical decision, architecture log, adr.
metadata:
  author: lkb-99
---

# Architecture Decision Record (ADR)

## Overview

This skill empowers Manus to understand, create, and manage Architecture Decision Records (ADRs). An ADR is a document that captures an important architectural decision made along with its context and consequences. The goal is to create a clear and lasting record of the key architectural choices that have shaped the system. By using this skill, you can ensure that the rationale behind significant design decisions is not lost over time, facilitating better communication, onboarding of new team members, and more consistent architectural evolution.

ADRs are particularly valuable in agile and evolving projects where design decisions are made incrementally. They provide a lightweight yet powerful way to document the "why" behind the "what," preventing the need for future architectural archaeology. This skill provides the structure, templates, and workflow to seamlessly integrate ADRs into your development process.

By leveraging this skill, teams can cultivate a culture of thoughtful architectural practice. It encourages deliberate decision-making and provides a mechanism for knowledge sharing that scales with the team and the complexity of the system. The result is a more robust, maintainable, and understandable architecture that can evolve gracefully over time.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: ADR, architecture decision record, architectural decision, design document, technical decision, architecture log, adr, registro de decisão de arquitetura, decisão de arquitetura, documento de design, decisão técnica.
- Phrases: "create a new ADR", "document an architectural decision", "criar um novo ADR", "documentar uma decisão de arquitetura", "gerenciar ADRs".
- Context: Any discussion about documenting software architecture, technical choices, or design rationale.

**Example user queries that trigger this skill:**
- "I need to create an ADR for our new database choice."
- "Can you help me document the decision to use microservices?"
- "Quero criar um registro de decisão de arquitetura para o nosso novo serviço."

## When to Use This Skill

This skill is most effective in the following scenarios:

*   **Making Significant Technology Choices:** When you need to decide on a new database, programming language, framework, or any other significant piece of technology.
*   **Defining Architectural Patterns:** When establishing a new pattern for the system, such as microservices, event-driven architecture, or a specific layering strategy.
*   **Changing Core Component Interfaces:** When a proposed change to a fundamental API or component interface will have a widespread impact.
*   **Addressing Critical Non-Functional Requirements:** When making a decision to meet a specific performance, security, scalability, or availability requirement.
*   **Adopting a New Standard or Protocol:** When introducing a new standard for communication, data format, or integration.
*   **Deprecating an Existing Solution:** When a decision is made to retire a legacy component or service, and the reasoning needs to be documented.
*   **Onboarding New Team Members:** To provide new developers with a clear history of the architectural evolution of the project.
*   **Resolving Architectural Debates:** To formally document the outcome of a debate and the reasons for the chosen path, even if there was no complete consensus.

## Core Capabilities

This skill provides a comprehensive toolkit for working with Architecture Decision Records.

### 1. ADR Creation

The skill can generate a new, pre-populated ADR file based on a standardized template. This ensures consistency across all records and guides the user to provide all necessary information.

**Example Interaction:**

```
User: Create a new ADR for 'Adopt PostgreSQL as the primary database'.
Manus: (Creates a new file named '0001-adopt-postgresql-as-the-primary-database.md' with the standard template)
```

### 2. ADR Template

The skill uses a default Markdown template for all new ADRs. This template is based on the format proposed by Michael Nygard, one of the early proponents of ADRs.

**Default Template:**

```markdown
# 1. Title of the decision

*   **Status:** [Proposed | Accepted | Deprecated | Superseded]
*   **Date:** [YYYY-MM-DD]

## Context

[Describe the context and problem that this decision addresses. This section should include the forces at play, including technological, political, social, and project local. It should also describe the problem that the decision is trying to solve.]

## Decision

[Describe our decision. This section should be a clear and concise statement of the decision that was made.]

## Consequences

[Describe the results of this decision. All consequences should be listed here, not just the positive ones. This includes any trade-offs that were made, any risks that were accepted, and any follow-up work that is now required.]
```

### 3. ADR Management

The skill can help you manage the lifecycle of your ADRs.

*   **List ADRs:** View a list of all existing ADRs, along with their status.
*   **View an ADR:** Display the content of a specific ADR.
*   **Update an ADR's Status:** Change the status of an ADR (e.g., from 'Proposed' to 'Accepted').

### 4. Knowledge Synthesis

The skill can analyze and synthesize information from multiple ADRs to provide a high-level overview of the system's architecture. For example, you can ask for a summary of all technology choices or a timeline of key architectural decisions.

**Example Interaction:**

```
User: Summarize the technology stack based on our ADRs.
Manus: (Analyzes all accepted ADRs and extracts the technologies mentioned in the 'Decision' section)

Based on the existing ADRs, here is a summary of the current technology stack:

*   **Database:** PostgreSQL (from ADR-0001)
*   **Inter-service Communication:** gRPC (from ADR-0002)
*   **Asynchronous Messaging:** RabbitMQ (from ADR-0003)
```

### 5. ADR Validation

The skill can perform validation checks on ADR files to ensure they conform to the expected format and contain all the necessary sections. This helps maintain the quality and consistency of the documentation.

**Example Interaction:**

```
User: Validate the ADR file '0004-use-react-for-frontend.md'.
Manus: (Checks the file for the required sections: Status, Date, Context, Decision, Consequences)

The ADR file '0004-use-react-for-frontend.md' is missing the 'Consequences' section. Please add it to complete the record.
```

## Step-by-Step Workflow

Here is a typical workflow for using this skill to create and manage ADRs:

1.  **Identify the Need for a Decision:** The process begins when a significant architectural decision needs to be made. This could be triggered by a new feature, a performance issue, or a desire to improve the existing architecture.

2.  **Initiate the ADR Process:** Use the skill to create a new ADR. Provide a descriptive title that summarizes the proposed decision.

    *   **Action:** `manus: adr new "Use gRPC for inter-service communication"`
    *   **Result:** Manus creates a new file, such as `0002-use-grpc-for-inter-service-communication.md`, with the status set to "Proposed".

3.  **Flesh out the Context:** Edit the newly created ADR file to provide detailed context. Describe the problem, the constraints, and the alternatives that were considered. This is a critical step for ensuring the decision is well-understood in the future.

    *   **Action:** Open the generated Markdown file and fill in the "Context" section.

4.  **Discuss and Debate:** Share the proposed ADR with the team. This can be done through a pull request, a team meeting, or any other collaborative forum. The goal is to gather feedback, identify potential issues, and build consensus.

5.  **Make the Decision:** Based on the discussion, make a final decision. This might be to accept the proposal, reject it, or choose an alternative.

6.  **Document the Decision and Consequences:** Update the ADR to reflect the final decision. Clearly state what was decided in the "Decision" section. In the "Consequences" section, document the expected outcomes, both positive and negative. This includes any new risks, trade-offs, or required follow-up work.

7.  **Update the Status:** Change the status of the ADR to "Accepted". If the proposal was rejected, you can either delete the ADR or move it to a "Rejected" or "Deprecated" status.

    *   **Action:** `manus: adr update-status 0002 Accepted`

8.  **Implement the Decision:** With the decision formally documented, the team can now proceed with implementation.

9.  **Revisit and Revise:** ADRs are not set in stone. If a past decision is no longer valid, create a new ADR to supersede it. The new ADR should reference the old one and explain why the decision is being changed. This creates a clear audit trail of architectural evolution.

## Best Practices

To get the most out of using Architecture Decision Records, follow these best practices:

*   **Keep it Lightweight:** The goal of ADRs is not to create a heavy, bureaucratic process. The process should be as simple and unobtrusive as possible. The focus is on the value of the documentation, not the ceremony of creating it.
*   **One Decision per ADR:** Each ADR should focus on a single, atomic decision. If you have multiple related decisions, create a separate ADR for each one and link them together.
*   **Write for the Future:** When writing an ADR, imagine you are writing it for a new team member who will join the project two years from now. Provide enough context and rationale for them to understand the decision without needing to ask anyone.
*   **Store ADRs with the Code:** ADRs should be stored in the same version control repository as the code they relate to. This makes them easy to find, and it ensures that the architectural documentation evolves along with the codebase. A common practice is to store them in a `doc/adr` or `docs/architecture/decisions` directory.
*   **Use a Clear Naming Convention:** Adopt a consistent naming convention for your ADR files. A simple and effective approach is to use a sequential number followed by a short, descriptive title (e.g., `0001-adopt-postgresql.md`).
*   **Automate Where Possible:** Use scripts or tools to automate the creation and management of ADRs. This can help to enforce consistency and reduce the friction of the process.
*   **Socialize the Process:** Make sure the entire team understands the purpose and value of ADRs. Encourage everyone to participate in the process, not just the senior architects.
*   **Don't Retrofit Everything:** While it can be tempting to go back and document all past decisions, it's often not a good use of time. Start by documenting new decisions as they are made. You can selectively document past decisions if they are particularly important or poorly understood.
*   **Use Plain Language:** Write ADRs in a clear and concise way that is easy for everyone to understand, regardless of their technical expertise. Avoid jargon where possible, or explain it if it's necessary.
*   **Focus on the Why, Not Just the What:** The most important part of an ADR is the "Context" section. It should clearly explain the problem and the reasons for the decision. The "what" is important, but the "why" is essential for long-term understanding.
*   **Embrace Asynchronicity:** The ADR process doesn't have to be synchronous. A pull request-based workflow is a great way to allow for asynchronous discussion and feedback from the entire team.
*   **Link to Other Documents:** If your decision is influenced by other documents, such as a business requirements document or a technical whitepaper, link to them from the ADR. This provides additional context for future readers.

## Examples

Here are a few examples of how this skill can be used in practice.

### Example 1: Creating a New ADR

**User Request:**

> "Manus, we need to decide on a message broker. Create an ADR for using RabbitMQ."

**Manus Action:**

1.  Creates a new file: `doc/adr/0003-use-rabbitmq-for-asynchronous-messaging.md`
2.  Populates the file with the standard ADR template.

**Generated ADR:**

```markdown
# 3. Use RabbitMQ for Asynchronous Messaging

*   **Status:** Proposed
*   **Date:** 2026-02-02

## Context

We have a growing need for asynchronous communication between our services. For example, when a new user signs up, we need to send them a welcome email, provision their account, and notify the analytics service. Currently, these actions are performed synchronously, which leads to long response times and tight coupling between services. We need a reliable and scalable message broker to decouple our services and improve performance.

We considered the following options:

*   **RabbitMQ:** A mature, feature-rich message broker with broad community support and client libraries for all our languages.
*   **Apache Kafka:** A high-throughput, distributed streaming platform. While powerful, it is more complex to set up and manage than RabbitMQ, and its log-based architecture is more than we need for our current use cases.
*   **AWS SQS:** A fully managed message queuing service. While easy to use, it would lock us into the AWS ecosystem, and we prefer to maintain a cloud-agnostic architecture at this stage.

## Decision

We will adopt RabbitMQ as our primary message broker for asynchronous inter-service communication. All new services that need to communicate asynchronously should use RabbitMQ.

## Consequences

*   **Positive:**
    *   Services will be more loosely coupled, improving resilience and scalability.
    *   User-facing response times will be reduced for operations that can be performed asynchronously.
    *   We can leverage the extensive features of RabbitMQ, such as message routing, persistence, and dead-letter exchanges.
*   **Negative:**
    *   We will need to host and manage a RabbitMQ cluster, which adds operational overhead.
    *   Developers will need to be trained on how to use RabbitMQ effectively.
    *   There is a risk of message loss if the broker is not configured correctly for high availability.
*   **Follow-up:**
    *   A new set of libraries and standards for interacting with RabbitMQ will be developed.
    *   The infrastructure team will be responsible for setting up and maintaining the RabbitMQ cluster.
    *   A training session will be scheduled for all developers.
```

### Example 2: Summarizing ADRs

**User Request:**

> "What architectural patterns have we adopted so far?"

**Manus Action:**

1.  Scans all ADRs with the status "Accepted".
2.  Looks for keywords related to architectural patterns in the "Title" and "Decision" sections.
3.  Synthesizes the findings into a summary.

**Manus Response:**

> Based on the accepted ADRs, we have adopted the following architectural patterns:
>
> *   **Microservices:** We are moving towards a microservice architecture to improve modularity and scalability (ADR-0002).
> *   **Asynchronous Messaging:** We use RabbitMQ for asynchronous communication between services (ADR-0003).
> *   **API Gateway:** All external traffic is routed through an API Gateway to provide a single entry point to our system (ADR-0005).

This skill is designed to be a living part of your development process, helping you build better software through clear, well-documented architectural decisions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
