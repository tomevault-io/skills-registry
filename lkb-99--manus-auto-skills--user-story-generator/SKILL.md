---
name: user-story-generator
description: "Use this skill when users want to create, write, or generate user stories, acceptance criteria, or agile development tasks. Triggers: user story, acceptance criteria, agile, backlog, sprint, Gherkin, BDD, feature, epic, INVEST."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# User Story Generator

## Overview
This skill is designed to assist software developers, product managers, and agile teams in generating well-structured and detailed user stories. A user story is a concise, plain-language description of a feature told from the perspective of the person who desires the new capability, usually a user or customer of the system. This skill helps in defining the "who," "what," and "why" of a requirement in a simple, yet powerful way. By providing a structured template, it ensures that all essential components of a user story, including acceptance criteria, are captured, leading to clearer requirements and smoother development cycles. This skill is not just a simple template filler; it leverages AI to understand the context of the feature and suggest relevant details, potential edge cases, and comprehensive acceptance criteria. It aims to be a collaborative partner in the requirement definition process, reducing the cognitive load on the product owner and the development team.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: user story, acceptance criteria, agile, backlog, sprint, Gherkin, BDD, feature, epic, INVEST, user persona, story points, agile development, product management, requisito, estória de usuário, critério de aceitação, metodologia ágil.
- Phrases: "criar uma user story", "escrever critérios de aceitação", "gerar uma estória de usuário", "preciso de ajuda com o backlog", "definir uma feature", "write a user story", "create acceptance criteria", "generate a user story", "help with the backlog", "define a feature".
- Context: Any discussion about writing, formatting, or managing user stories and agile development tasks.

**Example user queries that trigger this skill:**
- "Quero criar uma user story para a tela de login"
- "Como escrever bons critérios de aceitação?"
- "Preciso de um exemplo de user story com Gherkin"
- "Help me write a user story for the new search feature."

## When to Use This Skill
This skill is particularly useful in the following scenarios:

- **Initial Requirement Gathering:** When you need to translate high-level stakeholder needs and ideas into actionable and well-defined development items. The skill can take a vague feature request and help structure it into a formal user story.
- **Backlog Grooming and Refinement:** To add detail, clarity, and precision to existing user stories in the product backlog. It can help break down large, epic-level stories into smaller, more manageable ones.
- **Sprint Planning Meetings:** To ensure that the entire team has a clear and shared understanding of what needs to be built during an upcoming sprint. Using this skill during planning can help uncover hidden assumptions and dependencies.
- **Agile Coaching and Training:** To teach and enforce best practices for writing effective user stories. New team members or those new to agile can use this skill as a learning tool.
- **Solo Development Projects:** To maintain a structured and disciplined approach to building features even when working alone. It helps in thinking through the requirements from a user's perspective.
- **Product Discovery and Prototyping:** To quickly explore and define new product features and capabilities. The skill can generate multiple story variations to help in brainstorming and validating ideas.
- **API and Backend Development:** While user stories are often associated with UI features, this skill can be adapted to define requirements for backend services, APIs, and system-level functionalities by focusing on the "user" as another system or a developer.

## Core Capabilities

### 1. Structured User Story Generation
The primary capability of this skill is to generate a complete user story based on a simple prompt or a set of keywords. The skill will guide you to define the user role, the desired action, and the ultimate benefit.

**Standard Template (As a... I want to... so that...):**
```
As a [user role],
I want to [perform an action or achieve a goal],
so that I can [realize a benefit or value].
```

### 2. Comprehensive Acceptance Criteria Formulation
A user story is incomplete without clear, testable acceptance criteria. This skill excels at generating detailed acceptance criteria using the Gherkin syntax (Given/When/Then), which is widely used in Behavior-Driven Development (BDD).

**Gherkin-based Template:**
```markdown
**Acceptance Criteria:**

*   **Scenario 1:** [Clear and concise description of the scenario]
    *   **Given** [a specific precondition or context]
    *   **And** [another precondition, if necessary]
    *   **When** [a specific action is performed by the user or system]
    *   **Then** [an expected and observable outcome occurs]
    *   **And** [another expected outcome, if necessary]

*   **Scenario 2: (Edge Case)** [Description of an alternative or error scenario]
    *   **Given** [a different precondition]
    *   **When** [a different action is performed]
    *   **Then** [a different expected outcome is observed, e.g., an error message]
```

### 3. Adherence to Agile Principles
The skill is built upon industry best practices for writing effective user stories, most notably the **INVEST** principle. It encourages the creation of high-quality user stories that are:
- **I**ndependent: Can be developed and delivered separately.
- **N**egotiable: Not a strict contract, but a starting point for discussion.
- **V**aluable: Delivers clear value to the end-user or stakeholder.
- **E**stimable: Can be reasonably estimated by the development team.
- **S**mall: Sized appropriately to be completed within a single sprint.
- **T**estable: Has clear acceptance criteria that can be verified.

### 4. Template Customization and Expansion
The skill is not limited to a single template. It can adapt and provide additional sections to a user story as needed, such as:
- **Notes & Assumptions:** To capture important context or constraints.
- **Out of Scope:** To explicitly define what is not being built.
- **UI/UX Considerations:** To provide guidance on design and user experience.
- **Technical Notes:** For implementation details or dependencies.

## Step-by-Step Workflow

1.  **Initiate the Skill:** Start by calling the `user-story-generator` skill.
2.  **Provide a Feature Prompt:** Give the skill a high-level description of the feature you want to build. For example: `\"I need a login page for my e-commerce website.\"`
3.  **Define the User Role:** The skill will ask you to specify the user role. For the login page example, the role would be `\"registered customer\"`.
4.  **Specify the Action and Benefit:** The skill will then help you articulate the action and the benefit. For instance, `\"I want to log in with my email and password so that I can access my account and purchase history.\"`
5.  **Generate the Core User Story:** The skill will combine this information into a well-formatted user story.
6.  **Brainstorm Acceptance Criteria:** The skill will then prompt you to think about different scenarios. It might ask questions like:
    *   `\"What should happen if the user enters the correct credentials?\"`
    *   `\"What should happen if the password is incorrect?\"`
    *   `\"What about if the user's account is locked?\"`
    *   `\"Is there a 'Forgot Password' link?\"`
7.  **Formulate and Refine Criteria:** Based on your answers, the skill will generate detailed acceptance criteria in the Given/When/Then format for each scenario.
8.  **Add Supplementary Details:** The skill will ask if you want to add any other sections like 'Notes', 'UI/UX Considerations', or 'Technical Notes'.
9.  **Review and Finalize:** The skill will present the complete user story with all its components. You can then review, edit, and finalize it.
10. **Export or Copy:** The final output is a clean, well-formatted Markdown text that you can directly copy into your project management tool or save as a document.

## Best Practices

- **Be Specific in Your Prompts:** The more context you provide to the skill, the better and more relevant the generated story and criteria will be. Instead of `\"user profile\"`, try `\"As a user, I want to be able to edit my profile information, including my name, email, and profile picture.\"`
- **Collaborate with the Skill:** Treat the skill as a brainstorming partner. Use its questions and suggestions to think through the feature from all angles.
- **Focus on Value:** Always keep the `\"so that...\"` part of the story in mind. It represents the value proposition of the feature and should guide the development effort.
- **Keep Stories Small:** If the generated story seems too large or complex, use the skill to help you break it down into smaller, more manageable stories.
- **Review with the Team:** The generated user story is a starting point for a conversation. Always review it with the entire development and product team to ensure a shared understanding.
- **Don't Forget Non-Functional Requirements:** Use the 'Notes' or 'Technical Notes' sections to capture important non-functional requirements (NFRs) such as performance, security, or accessibility.

## Examples

### Example 1: E-commerce Search Functionality

**User Prompt:** `\"Generate a user story for a product search feature on an e-commerce site.\"`

**Generated SKILL.md Output:**

```markdown
**User Story**

**As a** shopper,
**I want to** search for products using keywords,
**so that I can** quickly find the items I am interested in purchasing.

---

**Acceptance Criteria:**

*   **Scenario 1: Successful Search with Multiple Results**
    *   **Given** I am on the homepage.
    *   **When** I enter the keyword `\"laptop\"` into the search bar and press Enter.
    *   **Then** I should be redirected to a search results page.
    *   **And** the page should display a list of products related to `\"laptop\"`.
    *   **And** the search term `\"laptop\"` should be visible in the search bar on the results page.

*   **Scenario 2: Search with No Results**
    *   **Given** I am on the homepage.
    *   **When** I enter a non-existent product keyword like `\"xyzabc\"` into the search bar and press Enter.
    *   **Then** the search results page should display a message indicating `\"No products found for 'xyzabc'\"`.
    *   **And** the page might suggest alternative search terms or categories.

*   **Scenario 3: Search with a Single Result**
    *   **Given** I am on the homepage.
    *   **When** I enter a unique product SKU like `\"MBP-M3-2024\"` into the search bar.
    *   **Then** the search results page should display the single product matching that SKU.

---

**UI/UX Considerations:**
- The search bar should be prominently displayed on all main pages.
- An autocomplete or search suggestion feature should be considered to help users.
- Search results should include a product image, name, price, and a short description.

**Technical Notes:**
- The search functionality should be case-insensitive.
- The search algorithm should handle partial matches and typos gracefully.
- The performance of the search should be optimized to return results in under 500ms.
```

### Example 2: API Endpoint for User Data

**User Prompt:** `\"Create a story for a backend API that provides user details.\"`

**Generated SKILL.md Output:**

```markdown
**User Story**

**As a** client application developer,
**I want to** make a GET request to a `/api/users/{id}` endpoint,
**so that I can** retrieve the profile information for a specific user.

---

**Acceptance Criteria:**

*   **Scenario 1: Successfully Retrieve a User**
    *   **Given** a user with ID `\"12345\"` exists in the database.
    *   **When** I send a GET request to `/api/users/12345` with a valid authentication token.
    *   **Then** the API should return a `200 OK` status code.
    *   **And** the response body should be a JSON object containing the user's id, name, email, and join_date.
    *   **And** the response body should NOT contain sensitive information like the user's password hash.

*   **Scenario 2: User Not Found**
    *   **Given** a user with ID `\"99999\"` does not exist in the database.
    *   **When** I send a GET request to `/api/users/99999`.
    *   **Then** the API should return a `404 Not Found` status code.

*   **Scenario 3: Unauthorized Access**
    *   **Given** a user with ID `\"12345\"` exists.
    *   **When** I send a GET request to `/api/users/12345` without a valid authentication token.
    *   **Then** the API should return a `401 Unauthorized` status code.

---

**Technical Notes:**
- The endpoint must be protected and require authentication.
- The JSON response structure should be documented in the API documentation.
- Consider implementing rate limiting to prevent abuse.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
