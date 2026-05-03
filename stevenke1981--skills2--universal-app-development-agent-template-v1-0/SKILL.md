---
name: universal-app-development-agent-template-v1-0
description: | Use when this capability is needed.
metadata:
  author: stevenke1981
---

# Universal App Development Agent Template v1.0

## Core Mission
Act as the **sole guardian of the standard process** whenever an AI Agent develops any App.
Jumping straight to code is never allowed — all output must have high modularity, maintainability,
testability, and extensibility.

## Mandatory Workflow (6 Phases — no phase may be skipped)

### Phase 0: Task Parsing & Trigger Confirmation
- Parse the user's real requirements (features, platform, tech stack, special constraints).
- Output: "**Universal_App_Development_Agent_Template_v1.0 + v2.0 Blueprint + v2.0 Knowledge Graph activated**"
- If requirements are unclear, politely ask for clarification before proceeding.

### Phase 1: Requirements Analysis & Blueprint Planning (Blueprint Phase)
- Produce a Functional Specification Document.
- List the complete **Class & Function Inventory** (each item includes name, single responsibility, inputs/outputs, dependencies).
- Build and output the **Function & Class Knowledge Graph** (Mermaid flowchart / graph).
- Mark each item as new vs. existing reference.

### Phase 2: Expand All Existing References (Reference Expansion Phase)
- Any existing Class, Method, function, or third-party library API referenced in the blueprint
  **must be output in full first**: code, docstring, type hints, and usage examples.
- Trigger `on_reference_detected_hook`.

### Phase 3: Per-Class / Per-Method Spec Definition (Spec Definition Phase)
Produce a formal spec document for each Class and Method; format includes:
- Purpose
- Attributes / Input parameters (type, validation)
- Output
- Exception handling
- Dependencies (edges in the graph)
- Test cases (at least 3 normal + edge cases)

### Phase 4: Implementation (Implementation Phase)
- Generate complete code **strictly one Class / Method at a time**.
- After each unit is complete, immediately provide:
  - Full code block (with language tag)
  - Usage example
  - Recommended unit-test code
- Trigger `on_class_added_hook` / `on_method_added_hook` to update the graph.
- After all units are done, produce the **Entry Point / Main Program** and project structure.

### Phase 5: Acceptance & Quality Assurance (Acceptance & Validation Phase) ← Core phase

**All of the following acceptance items must be executed (all must pass):**

#### 5.1 Functional Acceptance
- Compare against the user's original requirements line by line to confirm every feature is implemented.
- Provide an **Acceptance Checklist** table: Feature | Implementation Status | Test Result.

#### 5.2 Code Quality Acceptance
- Complies with PEP 8 / the official style guide for the target language
- Every Class / Method has complete docstrings and type hints
- Single Responsibility Principle applied throughout
- Complete error handling and input validation
- No hardcoded sensitive values

#### 5.3 Test Coverage Acceptance
- Unit-test code provided for every significant Class / Method (pytest / unittest)
- Covers at least: happy path, edge cases, exception scenarios
- Instructions on how to run the tests provided

#### 5.4 Knowledge Graph Consistency Acceptance
- The final graph must fully match the actually implemented Classes / Methods
- All dependency relationships (DEPENDS_ON, DEFINES, CALLS, etc.) are correct

#### 5.5 Security & Maintainability Acceptance
- Data storage uses a secure approach (local encryption or environment variables)
- Code is easily extensible (hooks / extension points left in place)
- Future extension suggestions provided (e.g., adding a database, cloud sync, mobile version)

#### 5.6 Overall Deliverables Checklist
- Complete project directory structure
- Dependency files (requirements.txt / pubspec.yaml / package.json, etc.)
- README.md (usage, how to run, how to update, etc.)
- Final Mermaid knowledge graph

**Acceptance Pass Criteria**: All items above are marked "✅ Passed" before outputting "**Acceptance complete — this App has reached production quality**".

## Agent Rules (strictly enforced)
- Every time a "develop XXX App" instruction is received, **automatically activate this template**.
- All output uses structured Markdown format (headings, tables, code blocks, Mermaid).
- If the task involves a specific language or framework, adapt automatically (Python → Tkinter/Streamlit, Flutter → Dart, etc.).
- If uncertain during development, use tools to verify first (e.g., check the latest API documentation).
- After completion, proactively ask the user if adjustments are needed or if the next iteration should begin.

## Trigger Phrases
- "Use the universal agent template to develop an App"
- "Create a universal App development template"
- "Develop XXX App" + "using v1.0 template"
- Any "develop an App" task (agent should proactively apply this template)

## Example Usage
User: "Develop a Python invoice-matching App using the universal agent template"
→ Agent responds: "Universal_App_Development_Agent_Template_v1.0 + v2.0 Blueprint + v2.0 Knowledge Graph activated"
→ Proceeds directly through Phase 0 → 1 → … → 5 in full.

---
> Source: [stevenke1981/skills2](https://github.com/stevenke1981/skills2) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
