---
name: design-skill
description: Use when working with a skill that creates comprehensive design documents based on requirements and research findings, transforming specifications into detailed technical designs ready for implementation
metadata:
  author: shsrams
---
# Overview

Develop a comprehensive design document that consolidates requirements and research into a detailed technical design with architecture, components, data models, and implementation guidance.

## Parameters
- requirements_source (required) - Path to the requirements document (e.g., `{project_dir}/requirements-elaboration.md`)
- research_dir (optional) - Path to directory containing research findings (e.g., `{project_dir}/research/`)
- project_dir (optional, default: derived from requirements_source parent directory) - The directory where the design document will be stored

### Rules for parameter acquisition
- You MUST ask for all required parameters upfront if not provided
- You MUST confirm the acquired parameters with the user before proceeding
- You MUST verify that the requirements_source file exists and is readable
- If project_dir is not provided, You MUST derive it from the parent directory of requirements_source (e.g., if requirements_source is `planning/requirements-elaboration.md`, then project_dir should be `planning`)
- You MUST create the project_dir if it doesn't exist
- You MUST NOT overwrite existing design files without user confirmation

## Steps
1. Design Preparation
Review requirements and research to prepare for design creation.

**Rules for execution**:
- You MUST read and understand all requirements from the requirements_source file
- You MUST read all research documents from research_dir if provided
- You MUST identify key requirements that need to be addressed in the design
- You MUST identify relevant research findings that inform design decisions
- You MUST summarize the scope and key considerations for the user
- You MUST ask the user if there are any additional considerations or constraints before proceeding

2. Design Document Structure Planning
Determine whether to create a single document or modular structure based on complexity.

**Rules for execution**:
- You SHOULD create modular documents for complex designs (multiple components, extensive requirements)
- You MAY create a single document for simple designs (few components, straightforward architecture)
- For modular structure, You MUST create:
  - {project_dir}/detailed-design.md (main navigation hub)
  - {project_dir}/requirements.md (detailed requirements)
  - {project_dir}/architecture.md (architecture and data flow)
  - {project_dir}/components.md (component specifications)
  - {project_dir}/testing-error-handling.md (testing strategy and error handling)
  - {project_dir}/appendices.md (technology choices, research findings, alternatives)
- For single document structure, You MUST include all sections in {project_dir}/detailed-design.md
- You MUST ask the user which structure they prefer before proceeding

3. Design Document Creation
Create comprehensive design documentation with all required sections.

**Rules for execution**:
- You MUST write the design as a standalone document that can be understood without reading other project files
- You MUST include the following sections (either in single document or split across modular documents):
  - Overview
  - Detailed Requirements (consolidated from requirements_source)
  - Architecture Overview
  - Components and Interfaces
  - Data Models
  - Error Handling
  - Testing Strategy
  - Appendices (Technology Choices, Research Findings, Alternative Approaches)
- You MUST consolidate all requirements from the requirements_source file into the Detailed Requirements section
- You MUST include an appendix section that summarizes key research findings, including:
  - Major technology choices with pros and cons
  - Existing solutions analysis
  - Alternative approaches considered
  - Key constraints and limitations identified during research
- You MUST generate mermaid diagrams for architectural overviews, data flow, and component relationships
- You MUST apply visual styling to mermaid diagrams using classDef for colors, stroke widths, and grouping
- You SHOULD include diagrams or visual representations when appropriate using mermaid syntax
- You MUST ensure the design addresses all requirements identified during the clarification process
- You SHOULD highlight design decisions and their rationales, referencing research findings where applicable
- You SHOULD organize the design logically with clear section headings and subsections
- You SHOULD use tables, bullet points, and formatting to improve readability
- You MUST include concrete examples (code snippets, data schemas, sample queries, test cases) rather than abstract descriptions
- For modular structure, You MUST add navigation links between documents (← Back, Previous, Next →)
- For modular structure, You MUST create a "Document Structure" section in detailed-design.md listing all documents with their key sections
- You MUST create a "Quick Reference" section in the main design document with:
  - Core technologies used
  - Deployment architecture summary
  - Performance targets
  - Key categories or workflows

3. Design Review and Iteration
Review the design with the user and refine based on feedback.

**Rules for execution**:
- You MUST present the completed design to the user
- You MUST highlight key design decisions and ask for feedback
- You MUST be prepared to iterate on the design based on user input
- You MUST offer to return to requirements clarification if gaps are identified during design
- You MUST offer to conduct additional research if new questions emerge during design
- You MUST ask the user if the design is complete and ready for implementation planning
- You MUST ask the user what they would like to do next:
  - Return to requirements clarification (if gaps identified)
  - Conduct additional research (if new questions emerged)
  - Proceed to implementation planning (if design is complete)
- If user chooses to proceed to implementation planning, you MUST use your 'write-plan' SKILL against the design document to create an implementation plan
- You MUST NOT automatically proceed to the next step without explicit user direction

## Key Principles
- Standalone clarity - Design should be understandable without external context
- Requirements traceability - All requirements must be addressed in the design
- Visual communication - Use styled diagrams to clarify complex concepts
- Decision documentation - Explain why choices were made, not just what was chosen
- Research integration - Incorporate findings from research into design rationale
- Iterative refinement - Be prepared to revise based on feedback
- Concrete over abstract - Provide actual examples (code, schemas, queries) rather than descriptions
- Modular organization - Split complex designs into focused documents with clear navigation
- Quick reference - Include summary section for fast lookup of key information
- YAGNI ruthlessly - Remove unnecessary features from all designs
- SOLID principles - Apply SOLID design principles for maintainable architecture

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
