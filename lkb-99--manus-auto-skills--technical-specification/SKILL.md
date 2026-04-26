---
name: technical-specification
description: "Creates detailed technical specification documents. Use this skill when users need to write technical specs, design documents, or outline system architecture. Triggers: technical specification, spec, tech spec, software design, system architecture, API design, feature documentation, documentação técnica, especificação técnica."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Technical Specification Documentation

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: technical specification, spec, tech spec, software design document, system architecture, API design, feature documentation, documentação técnica, especificação técnica, desenho de software, desenho de sistema, documento de arquitetura
- Phrases: "create a technical spec", "write a design document", "document the system architecture", "how to write a technical specification", "criar uma especificação técnica", "escrever um documento de desenho", "documentar a arquitetura do sistema"
- Context: Any discussion about creating formal documentation for software, systems, features, or APIs before or during development.

**Example user queries that trigger this skill:**
- "I need to write a technical specification for a new mobile app."
- "Como eu crio um documento de arquitetura para o meu novo serviço?"
- "Can you help me outline the API design for our new feature?"

## Overview
This skill is designed to assist developers, project managers, and system architects in creating comprehensive and structured technical specification documents. A technical specification is a critical document that describes the technical requirements, design, and implementation details of a software system, feature, or component. It serves as a blueprint for the development team and a reference for all stakeholders. This skill provides a standardized template and a step-by-step workflow to ensure that all essential aspects of a technical design are covered, leading to better-aligned teams, fewer ambiguities, and a more efficient development process.

## When to Use This Skill

**ALWAYS use this skill when user mentions:**
- Creating or writing technical specifications
- Writing design documents
- Documenting system architecture plans
- Outlining new feature documentation
- Defining API design specifications
- Preparing technical proposals (RFP)
- Onboarding developers with technical documentation

## Core Capabilities

### 1. Structured Document Generation
This skill provides a robust template that covers all the necessary sections of a professional technical specification document. It guides the user to fill in the details for each section, ensuring a complete and logical structure.

#### Key Sections:
-   **Introduction:** Purpose, scope, definitions, and references.
-   **System Overview:** High-level description of the system and its goals.
-   **Functional Requirements:** What the system must do, described in detail.
-   **Non-Functional Requirements:** Performance, security, scalability, and other quality attributes.
-   **System Architecture:** High-level design, components, and their interactions.
-   **Data Design:** Database schema, data models, and data flow.
-   **Interface Design:** User interfaces (UI), APIs, and other system interfaces.
-   **Implementation Details:** Technology stack, coding standards, and deployment plan.
-   **Testing and Validation:** Testing strategy, test cases, and acceptance criteria.

### 2. Requirements Elicitation and Analysis
The skill can help in brainstorming and refining both functional and non-functional requirements. By prompting the user with specific questions, it ensures that requirements are clear, testable, and unambiguous.

#### Example Prompts:
-   *"Describe the user journey for the new feature from login to completion."*
-   *"What is the expected response time for the main API endpoint under a load of 1000 concurrent users?"*
-   *"What are the data privacy and compliance requirements (e.g., GDPR, HIPAA)?"*

### 3. Architecture and Design Modeling
While this skill does not generate diagrams directly, it provides sections and templates for describing architectural patterns (e.g., Microservices, Monolith), data models, and component interactions. It encourages the use of standard modeling languages like UML and provides placeholders for embedding diagrams.

#### Template for a Microservice:
```markdown
**Service Name:** User Authentication Service

**Description:** Manages user registration, login, and session management.

**Endpoints:**
- `POST /register`
- `POST /login`
- `POST /logout`

**Data Storage:** Uses a PostgreSQL database to store user credentials.

**Dependencies:** None
```

### 4. Template Customization
The provided template is a starting point. The skill allows for easy customization by adding, removing, or modifying sections to fit the specific needs of a project. The use of Markdown makes it simple to edit and version control the document.

## Step-by-Step Workflow

1.  **Initialize the Document:** Start by creating a new Markdown file using the base template provided by this skill.
    ```bash
    # Create a new file for your technical specification
    touch my-project-spec.md
    ```

2.  **Fill in the Frontmatter:** Edit the YAML frontmatter to set the name, description, and other metadata for your document.

3.  **Complete the Introduction:**
    -   **Purpose:** Clearly state the objective of the document and the system/feature it describes.
    -   **Scope:** Define the boundaries of the system. What is included and what is not?
    -   **Definitions, Acronyms, and Abbreviations:** Create a glossary of terms to ensure clear communication.
    -   **References:** List any other relevant documents, such as product requirements documents (PRDs) or market research.

4.  **Define Requirements:**
    -   **Functional Requirements:** Use a structured format (e.g., user stories) to list all the functionalities. Assign a unique ID to each requirement for traceability.
        -   **Template:** `FR-001: As a [user type], I want to [perform an action] so that I can [achieve a goal].`
    -   **Non-Functional Requirements:** Detail the system's quality attributes.
        -   **Performance:** e.g., Response time, throughput.
        -   **Scalability:** e.g., Number of concurrent users, data volume growth.
        -   **Security:** e.g., Authentication, authorization, data encryption.
        -   **Usability:** e.g., Accessibility standards, user-friendliness.
        -   **Reliability:** e.g., Uptime, mean time between failures (MTBF).

5.  **Design the System Architecture:**
    -   Provide a high-level overview of the architecture.
    -   Use diagrams (e.g., C4 model, UML component diagrams) to visualize the structure. You can create these using external tools and embed them as images.
    -   Describe each major component and its responsibilities.

6.  **Detail the Data Design:**
    -   Define the database schema, including tables, columns, data types, and relationships.
    -   Describe the data models and objects used in the application logic.
    -   Illustrate the data flow through the system.

7.  **Specify the Interface Design:**
    -   **User Interface (UI):** Include wireframes or mockups. Describe the user interaction flow.
    -   **Application Programming Interface (API):** For each endpoint, specify the HTTP method, URL, request/response format (JSON schema), and authentication requirements.

8.  **Outline the Implementation Plan:**
    -   **Technology Stack:** List all the languages, frameworks, and tools that will be used.
    -   **Coding Standards:** Reference your team's coding style guide.
    -   **Deployment:** Describe the CI/CD pipeline, hosting environment, and release process.

9.  **Define the Testing Strategy:**
    -   Outline the approach for unit tests, integration tests, and end-to-end tests.
    -   Provide examples of key test cases and define the acceptance criteria for the project to be considered complete.

10. **Review and Refine:**
    -   Share the document with all stakeholders (developers, QA, product managers) for feedback.
    -   Iterate on the document until a consensus is reached.
    -   Use a version control system like Git to track changes.

## Best Practices

-   **Be Clear and Unambiguous:** Use precise language. Avoid jargon where possible, or define it in the glossary.
-   **Keep it Updated:** A technical specification is a living document. It should be updated as the system evolves.
-   **Use Visuals:** Diagrams, charts, and mockups are often more effective than text for explaining complex ideas.
-   **Focus on the "What," not the "How":** For requirements, describe what the system should do, not how it should be implemented. The "how" belongs in the design and implementation sections.
-   **Prioritize Requirements:** Use labels like `Must-Have`, `Should-Have`, `Could-Have` to indicate the priority of requirements.
-   **Version Control:** Store your specification in a version control system (e.g., Git) to manage its history and collaborate with others.
-   **Review and Approve:** Establish a formal review and approval process to ensure all stakeholders are in agreement before development starts.

## Examples

### Example: API Endpoint Specification

**Endpoint:** `POST /api/v1/users`

**Description:** Creates a new user in the system.

**Request Body:**
```json
{
  "username": "string (required, min 3 chars, max 50 chars)",
  "email": "string (required, valid email format)",
  "password": "string (required, min 8 chars, at least one number and one special character)"
}
```

**Success Response (201 Created):**
```json
{
  "userId": "uuid",
  "username": "string",
  "email": "string",
  "createdAt": "timestamp"
}
```

**Error Responses:**
-   **400 Bad Request:** If the request body is invalid.
    ```json
    {
      "error": "Invalid input",
      "details": {
        "username": "Username is already taken."
      }
    }
    ```
-   **500 Internal Server Error:** If an unexpected error occurs on the server.

### Example: Non-Functional Requirement

**ID:** NFR-SEC-01

**Requirement:** User Password Storage

**Description:** All user passwords must be stored in the database in a hashed and salted format. The hashing algorithm must be a modern, strong algorithm such as Argon2 or bcrypt.

**Rationale:** To prevent unauthorized access to user accounts in the event of a database breach.

**Acceptance Criteria:** A security audit confirms that passwords are not stored in plaintext and that a strong, salted hashing algorithm is used.

## References

-   [The C4 model for visualizing software architecture](https://c4model.com/)
-   [UML Distilled: A Brief Guide to the Standard Object Modeling Language by Martin Fowler](https://www.amazon.com/UML-Distilled-Standard-Modeling-Language/dp/0321193687)
-   [OpenAPI Specification](https://swagger.io/specification/)
-   [RFC 2119: Key words for use in RFCs to Indicate Requirement Levels](https://www.ietf.org/rfc/rfc2119.txt)
-   [Atlassian Guide on Writing Technical Specifications](https://www.atlassian.com/team-playbook/plays/technical-specs)


## Advanced Topics

### 1. System Decomposition
For large systems, it's crucial to break them down into smaller, manageable components. This section of the technical specification should detail the decomposition strategy.

- **Decomposition Strategy:** Explain the rationale behind the chosen decomposition (e.g., by business capability, by domain). Reference architectural patterns like Domain-Driven Design (DDD).
- **Component Catalog:** Provide a list of all components, their responsibilities, and the APIs they expose.
- **Communication Patterns:** Describe how components will communicate with each other (e.g., synchronous REST calls, asynchronous messaging via a message broker like RabbitMQ or Kafka).

### 2. Data Migration
When a new system replaces an old one, or when the database schema changes significantly, a data migration plan is necessary.

- **Migration Strategy:** Describe the overall approach (e.g., big bang migration, phased migration).
- **Data Mapping:** Provide a detailed mapping of data from the old schema to the new schema.
- **Migration Scripts:** Outline the steps to be performed by the migration scripts, including data transformation and validation.
- **Rollback Plan:** A critical part of any migration is the plan to roll back to the previous state in case of failure.

### 3. Security in Depth
This section should go beyond the basics of authentication and authorization and cover a broader range of security concerns.

- **Threat Model:** Identify potential threats and vulnerabilities using a framework like STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege).
- **Security Controls:** For each threat, describe the mitigation measures that will be implemented (e.g., input validation to prevent SQL injection, rate limiting to prevent DoS attacks).
- **Data Encryption:** Specify the requirements for data at rest and data in transit encryption. Mention the algorithms and key management procedures.
- **Auditing and Logging:** Define what security-sensitive events must be logged and how the logs will be stored and monitored.

### 4. Performance and Scalability
This section details how the system will meet its performance and scalability goals.

- **Performance Targets:** Define specific, measurable, achievable, relevant, and time-bound (SMART) performance goals (e.g., "The search API endpoint must return results in under 500ms for 95% of requests").
- **Scalability Strategy:** Describe how the system will handle increased load (e.g., horizontal scaling by adding more instances, vertical scaling by increasing the resources of existing instances).
- **Caching Strategy:** Detail the use of caching (e.g., in-memory cache like Redis, content delivery network like Cloudflare) to improve performance.
- **Load Testing Plan:** Outline the plan for load testing the system to ensure it meets the performance targets.

## More Examples

### Example: Frontend Component Specification

**Component:** `DataGrid`

**Description:** A reusable React component for displaying data in a sortable, filterable, and paginated table.

**Props (API):**
- `columns`: `Array<Object>` (required) - Defines the columns of the grid. Each object should have `key`, `title`, and `sortable` properties.
- `data`: `Array<Object>` (required) - The data to be displayed.
- `onRowClick`: `Function` (optional) - A callback function to be executed when a row is clicked.

**State:**
- `currentPage`: `number`
- `sortColumn`: `string`
- `sortDirection`: `'asc' | 'desc'`
- `filters`: `Object`

**Behavior:**
- Users can sort the data by clicking on column headers.
- Users can filter the data using a search input for each column.
- Pagination controls are displayed at the bottom of the grid.

### Example: Database Schema Change

**Change ID:** DB-CHG-005

**Description:** Add a `last_login_ip` column to the `users` table to enhance security auditing.

**DDL (Data Definition Language):**
```sql
ALTER TABLE users
ADD COLUMN last_login_ip VARCHAR(45);
```

**Impact:**
- The application logic for user login needs to be updated to capture and store the IP address.
- No immediate impact on existing data, the new column will be `NULL` for existing users until their next login.

**Rollback:**
```sql
ALTER TABLE users
DROP COLUMN last_login_ip;
```

## Additional Best Practices

-   **Involve the Whole Team:** The technical specification should not be written in isolation by a single person. Involve developers, QA engineers, and DevOps in the writing and review process to get diverse perspectives and ensure buy-in.
-   **Use a Template Engine:** For creating multiple similar specifications, consider using a template engine (e.g., Cookiecutter) to automate the creation of the document structure.
-   **Link to a Live Style Guide:** If your project has a frontend component library, link to a live style guide (e.g., Storybook) from your technical specification. This allows stakeholders to see and interact with the UI components.
-   **Automate Documentation Generation:** For APIs, use tools like Swagger or Postman to generate and maintain the API documentation automatically from the code annotations. This ensures that the documentation is always up-to-date.

## More References

-   [Google's Engineering Practices Documentation](https://google.github.io/eng-practices/)
-   [The Twelve-Factor App](https://12factor.net/)
-   [Microsoft's REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md)
-   [A Guide to Writing Good Technical Design Docs](https://www.freecodecamp.org/news/how-to-write-a-good-software-design-document/)


## Troubleshooting

This section provides guidance on common issues that may arise during the creation and use of the technical specification document.

- **Incomplete Requirements:**
  - **Symptom:** The development team is frequently asking for clarification on features, or there are many edge cases that were not considered.
  - **Solution:** Revisit the requirements elicitation phase. Conduct workshops with stakeholders to flesh out the user stories and acceptance criteria. Use techniques like the "5 Whys" to get to the root of what the user needs.

- **Outdated Document:**
  - **Symptom:** The implementation in the codebase has diverged significantly from what is described in the specification.
  - **Solution:** Implement a process for keeping the document up-to-date. This could be part of the definition of done for a task (i.e., a task is not complete until the documentation is updated). For API documentation, use tools that generate documentation from code comments to ensure it's always synchronized.

- **Disagreements Among Stakeholders:**
  - **Symptom:** There are conflicting opinions on how a feature should be implemented, or what the architecture should be.
  - **Solution:** The technical specification should be used as a tool for building consensus. Organize review meetings where all stakeholders can voice their concerns. Use a Designated Responsible Individual (DRI) to make the final decision if a consensus cannot be reached.

- **Overly Detailed or Too Vague:**
  - **Symptom:** The specification is either a huge, unreadable document that no one wants to touch, or it's so high-level that it doesn't provide enough guidance to the developers.
  - **Solution:** Strive for the right level of detail. The specification should be detailed enough to guide implementation without being overly prescriptive. For example, it should specify *what* a component does, but not the exact implementation details of every single function. Use appendices for highly detailed information that is not essential for the main body of the document.

## Glossary

- **API (Application Programming Interface):** A set of rules and protocols for building and interacting with software applications.
- **CI/CD (Continuous Integration/Continuous Deployment):** A set of practices that automate the building, testing, and deployment of software.
- **CRUD (Create, Read, Update, Delete):** The four basic functions of persistent storage.
- **DDD (Domain-Driven Design):** A software design approach that focuses on modeling the software to match the domain it's intended for.
- **RFP (Request for Proposal):** A document that solicits proposals, often made through a bidding process, by an agency or company interested in procurement of a commodity, service, or valuable asset, to potential suppliers to submit business proposals.
- **SLA (Service-Level Agreement):** A commitment between a service provider and a client. Particular aspects of the service – quality, availability, responsibilities – are agreed between the service provider and the service user.
- **UML (Unified Modeling Language):** A general-purpose, developmental, modeling language in the field of software engineering that is intended to provide a standard way to visualize the design of a system.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
