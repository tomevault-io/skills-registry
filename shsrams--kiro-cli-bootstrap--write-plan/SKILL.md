---
name: write-plan-skill
description: Creates structured implementation plans from design documents, breaking down complex designs into incremental, test-driven development steps following YAGNI, TDD, SOLID and DRY principles
metadata:
  author: shsrams
---

# Overview

Transform a detailed design document into a structured implementation plan with incremental steps that build working, demoable functionality following test-driven development and agile best practices.

## Parameters
- design_source (required) - Path to the design document (e.g., `{project_dir}/design/detailed-design.md` or `{project_dir}/detailed-design.md`)
- project_dir (optional, default: derived from design_source parent directory) - The directory where the implementation plan will be stored

### Rules for parameter acquisition
- You MUST ask for all required parameters upfront if not provided
- You MUST confirm the acquired parameters with the user before proceeding
- You MUST verify that the design_source file exists and is readable
- If project_dir is not provided, You MUST derive it from the parent directory of design_source
- You MUST create the project_dir/implementation/ directory if it doesn't exist
- You MUST NOT overwrite existing implementation plans without user confirmation

## Steps

1. Design Analysis and Planning Preparation
Review the design document to understand scope and complexity for implementation planning.

**Rules for execution**:
- You MUST read and thoroughly understand the design document from design_source
- You MUST identify all components, interfaces, and requirements that need implementation
- You MUST identify dependencies between components and logical implementation order
- You MUST assess the complexity to determine appropriate step granularity
- You MUST summarize the design scope and key implementation challenges for the user
- You MUST ask the user if there are any specific implementation priorities or constraints before proceeding

2. Implementation Plan Creation
Create a structured implementation plan following TDD and agile principles.

**Rules for execution**:
- You MUST create an implementation plan at {project_dir}/implementation/plan.md
- You MUST include a checklist at the beginning of the plan.md file to track implementation progress
- You MUST use the following specific instructions when creating the implementation plan:
  ```
  Convert the design into a series of implementation steps that will build each component in a test-driven manner following agile best practices. Each step must result in a working, demoable increment of functionality. Prioritize best practices, incremental progress, and early testing, ensuring no big jumps in complexity at any stage. Make sure that each step builds on the previous steps, and ends with wiring things together. There should be no hanging or orphaned code that isn't integrated into a previous step.
  ```
- You MUST format the implementation plan as a numbered series of detailed steps
- Each step in the plan MUST be written as a clear implementation objective
- Each step MUST begin with "Step N:" where N is the sequential number
- You MUST ensure each step includes:
  - A clear objective
  - General implementation guidance following SOLID principles
  - Test requirements emphasizing TDD approach
  - How it integrates with previous work
  - **Demo** - explicit description of the working functionality that can be demonstrated after completing this step
- You MUST ensure each step results in working, demoable functionality that provides value
- You MUST sequence steps so that core end-to-end functionality is available as early as possible
- You MUST apply YAGNI principles by excluding unnecessary features and over-engineering
- You MUST ensure steps follow SOLID design principles for maintainable code
- You MUST promote DRY principles by identifying reusable components early
- You MUST NOT include excessive implementation details that are already covered in the design document
- You MUST assume that all context documents (requirements, design, research) will be available during implementation
- You MUST break down the implementation into discrete, manageable steps
- You MUST ensure each step builds incrementally on previous steps
- You MUST prioritize test-driven development throughout
- You MUST ensure the plan covers all aspects of the design
- You MUST sequence steps to validate core functionality early
- You MUST ensure the checklist items correspond directly to the steps in the implementation plan

3. Plan Review and Finalization
Review the implementation plan with the user and refine based on feedback.

**Rules for execution**:
- You MUST present the completed implementation plan to the user
- You MUST highlight the implementation approach and key milestones
- You MUST explain how the plan follows TDD, SOLID, DRY, and YAGNI principles
- You MUST ask for feedback on the plan structure and step sequence
- You MUST be prepared to iterate on the plan based on user input
- You MUST ask the user if the implementation plan is ready for execution
- You MUST ask the user what they would like to do next:
  - Begin implementation following the plan
  - Modify the plan based on new requirements
  - Return to design refinement if gaps are identified
  - Create detailed step guides for implementation
- If user chooses to create step guides, you MUST use your 'create-step-guide' SKILL for each step in the implementation plan
- You MUST NOT automatically proceed to implementation without explicit user direction

## Key Principles
- Incremental value delivery - Each step must produce working, demoable functionality
- Test-driven development - Tests guide implementation and ensure quality
- YAGNI ruthlessly - Build only what's needed, avoid over-engineering
- SOLID design principles - Create maintainable, extensible code architecture
- DRY implementation - Identify and reuse common patterns and components
- Early integration - Wire components together frequently to avoid orphaned code
- Risk mitigation - Tackle complex or uncertain areas early in the process
- Clear objectives - Each step has explicit goals and success criteria
- Dependency management - Sequence steps to minimize blocking dependencies
- Continuous validation - Regular demos ensure implementation meets requirements

## Example Output

### Input Parameters
- design_source: `planning/design/detailed-design.md`
- project_dir: `planning`

### Generated Implementation Plan Structure

```markdown
# Customer Feedback Analysis System - Implementation Plan

## Implementation Checklist
- [ ] Step 1: Set up core data models and validation
- [ ] Step 2: Implement search functionality with Knowledge Base integration
- [ ] Step 3: Create visualization tool for chart generation
- [ ] Step 4: Add comprehensive error handling
- [ ] Step 5: Implement end-to-end testing

## Implementation Steps

### Step 1: Set up core data models and validation
Create the foundational data structures and validation logic following SOLID principles.

**Objective**: Establish type-safe data models with comprehensive validation.

**Implementation Guidance**:
1. Create Pydantic v2 models with `ConfigDict(frozen=True)` for immutability
2. Implement validation for required fields and business rules
3. Set up test framework with pytest and coverage reporting
4. Create placeholder service interfaces following dependency inversion

**Test Requirements**:
- Unit tests for model validation covering edge cases
- Tests for serialization/deserialization
- Validation error handling tests

**Integration**: Models serve as foundation for all subsequent components.

**Demo**: Working data models that validate input, handle errors gracefully, and provide clear error messages for invalid data.

### Step 2: Implement search functionality with Knowledge Base integration
Build the core search capability using AWS Bedrock Knowledge Base.

**Objective**: Enable semantic search across customer feedback data sources.

**Implementation Guidance**:
1. Implement Bedrock Agent Runtime client using centralized session factory
2. Add business function filtering with metadata-based mapping
3. Create citation extraction with source traceability
4. Implement retry logic with exponential backoff
5. Follow single responsibility principle with separate search and citation classes

**Test Requirements**:
- Unit tests with mocked Bedrock responses
- Integration tests with real Knowledge Base
- Performance tests validating sub-second response times
- Error handling tests for service failures

**Integration**: Uses data models from Step 1, provides foundation for visualization.

**Demo**: Functional search that returns relevant results with citations, handles errors gracefully, and performs within acceptable time limits.

### Step 3: Create visualization tool for chart generation
Implement chart specification generation for data visualization.

**Objective**: Generate Recharts-compatible JSON specifications from search results.

**Implementation Guidance**:
1. Create ChartSpecification model with validation
2. Implement data aggregation and sampling logic (max 500 points)
3. Support line charts (temporal) and bar charts (categorical)
4. Add data validation and error handling for malformed data
5. Apply DRY principles for common chart configuration patterns

**Test Requirements**:
- Unit tests for chart specification generation
- Tests for data sampling and aggregation
- Validation tests for chart configuration
- Integration tests with search results

**Integration**: Consumes search results from Step 2, integrates with frontend rendering.

**Demo**: Working chart generation that creates valid specifications, handles large datasets through sampling, and renders properly in frontend.
```

This example demonstrates the skill's output format with:
- Clear checklist for tracking progress
- Numbered steps with specific objectives
- Implementation guidance following SOLID/TDD/YAGNI principles
- Test requirements for each step
- Integration points between steps
- Demo descriptions showing working functionality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
