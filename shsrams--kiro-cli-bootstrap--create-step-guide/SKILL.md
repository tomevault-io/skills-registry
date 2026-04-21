---
name: create-step-guide-skill
description: Creates comprehensive implementation guides for individual steps from implementation plans, breaking down complex steps into granular tasks with clear acceptance criteria and developer context
metadata:
  author: shsrams
---

# Overview

Transform a high-level implementation step into a comprehensive, developer-ready implementation guide with granular tasks, clear acceptance criteria, and complete context.

## Parameters
- step_number (required) - The step number from the implementation plan (e.g., "1", "3", "7")
- plan_source (required) - Path to the implementation plan (e.g., `{project_dir}/implementation/plan.md`)
- project_dir (optional, default: derived from plan_source parent directory) - The project directory containing all context documents
- output_dir (optional, default: `{project_dir}/implementation/guides/`) - Directory where the step guide will be created

### Rules for parameter acquisition
- You MUST ask for all required parameters upfront if not provided
- You MUST confirm the acquired parameters with the user before proceeding
- You MUST verify that the plan_source file exists and contains the specified step
- You MUST create the output_dir if it doesn't exist
- You MUST NOT overwrite existing step guides without user confirmation

## Steps

1. Context Gathering and Analysis
Collect all relevant project context and analyze the target implementation step.

**Rules for execution**:
- You MUST read the implementation plan and identify the target step
- You MUST read all available context documents in the project directory:
  - Requirements documents (requirements-elaboration.md, requirements.md)
  - Design documents (detailed-design.md, architecture.md)
  - Research findings (research/ directory)
  - Previous step guides for dependencies
- You MUST analyze the step's objective, scope, and integration points
- You MUST identify dependencies from previous steps and requirements for future steps
- You MUST summarize the context and step scope for user confirmation

2. Step Guide Creation
Create a comprehensive implementation guide following the proven structure.

**Rules for execution**:
- You MUST create the step guide at `{output_dir}/step-{step_number}-guide.md`
- You MUST follow this structure:

```markdown
# Step {N} Implementation Guide: {Step Title}

## Overview
**Objective**: {Clear one-sentence objective}
**Duration**: {Estimated time}
**Prerequisites**: {Required knowledge/tools}
**Success Criteria**: {Measurable success criteria}

**Implementation Status**: {Progress tracking with checkboxes}
- [ ] Task 1: {Task title}
- [ ] Task 2: {Task title}

**Implementation Observations**: {Key learnings and technical decisions}

## Project Context
### Business Context
{What problem this solves, end users, system fit, business value}

### Technical Context
{Architecture overview, data flow, integration points, performance requirements}
**Architecture Overview**:
```
{Simple text-based flow diagram}
```

**Integration Points**:
- Input: {What this step receives}
- Output: {What this step produces}
- Dependencies: {What it depends on}

### Required Reading
**Essential Documents** (Read in this order):
1. **{file_path}** - Focus: {what to focus on}
   - Key takeaway: {main insight}

**Supporting Documents** (Reference as needed):
2. **{file_path}** - Context: {when to reference}

## Data Structure Understanding
### Input Data Format
{Concrete examples of expected inputs}

### Output Data Format  
{Concrete examples of expected outputs}

### Key Data Transformations
{How data flows and transforms through the step}

## Implementation Tasks
### Task 1: {Action-oriented title}
**Objective**: {One sentence goal}

**What to Implement:**
- [ ] Specific deliverable 1
- [ ] Specific deliverable 2

**Key Requirements:**
- Technical constraint 1
- Integration requirement 1
- Performance requirement 1

**Implementation Location**: `{exact_file_path}`

**Code to Add/Modify**:
```python
{Specific code examples or patterns}
```

**Acceptance Criteria:**
- [ ] Testable criterion 1
- [ ] Testable criterion 2
- [ ] Performance benchmark met

**Test Strategy**:
- Unit tests: {specific test types}
- Integration tests: {integration scenarios}
- Performance tests: {timing requirements}

{Continue for all tasks...}

## Best Practices to Follow
### Code Quality Standards
{Coding style, documentation, type safety, error handling}

### Development Process Standards
{TDD requirements, testing structure, code review criteria}

### Architecture Standards
{Design patterns, performance, security, scalability}

## Acceptance Criteria Summary
### Functional Requirements
- [ ] Core functionality works as specified
- [ ] All data processing requirements met
- [ ] Integration points function correctly

### Technical Requirements
- [ ] Performance benchmarks met ({specific timing})
- [ ] Code quality standards passed
- [ ] Security requirements addressed

### Documentation Requirements
- [ ] All documentation complete
- [ ] Code examples work as written
- [ ] API documentation updated

### Quality Requirements
- [ ] Test coverage meets standards ({percentage})
- [ ] Error handling comprehensive
- [ ] Ready for next development phase

## Implementation Notes
{Technical decisions, patterns discovered, gotchas, recommendations}

## Handoff Checklist
- [ ] All deliverables completed
- [ ] Integration points validated
- [ ] Performance requirements verified
- [ ] Documentation reviewed
- [ ] Tests passing
- [ ] Ready for next development phase

## Next Steps Preview
{What comes after this implementation, dependencies created, capabilities enabled}
```

- You MUST break down the step into tasks of 2-4 hours each
- You MUST include specific file paths for implementation locations
- You MUST provide concrete code examples and patterns
- You MUST include performance requirements with specific timing
- You MUST make acceptance criteria binary (pass/fail)
- You MUST include implementation status tracking with checkboxes
- You MUST add architecture diagrams using simple text-based flow
- You MUST include data structure examples with concrete formats
- You MUST reference specific line numbers in required reading when helpful

3. Guide Review and Validation
Review the implementation guide with the user and refine based on feedback.

**Rules for execution**:
- You MUST present the completed step guide to the user
- You MUST highlight the task breakdown and key acceptance criteria
- You MUST explain how the guide ensures consistent implementation quality
- You MUST ask for feedback on task granularity and completeness
- You MUST be prepared to iterate on the guide based on user input
- You MUST ask the user if the step guide is ready for developer use
- You MUST ask the user what they would like to do next:
  - Create guides for additional steps
  - Begin implementation using this guide
  - Refine the guide based on new requirements
- You MUST NOT automatically proceed without explicit user direction

## Key Principles
- Self-contained guidance - Guide includes all context needed for implementation
- Granular task breakdown - Tasks are 2-4 hours with clear deliverables
- Binary acceptance criteria - Pass/fail criteria that can be objectively verified
- Developer experience focus - Can be followed by someone new to the project
- Quality assurance - Comprehensive standards and validation requirements
- Integration awareness - Clear dependencies and handoff requirements
- Continuous validation - Regular checkpoints ensure progress toward objectives
- Best practices enforcement - Coding standards and architectural patterns included
- Progress tracking - Implementation status and task completion visibility
- Concrete examples - Specific code patterns and data structure examples
- Performance focus - Clear timing and performance requirements

## Example Output

### Input Parameters
- step_number: "2"
- plan_source: `planning/implementation/plan.md`
- project_dir: `planning`

### Generated Step Guide Structure

```markdown
# Step 2 Implementation Guide: Implement Search Functionality

## Overview
**Objective**: Create semantic search capability using AWS Bedrock Knowledge Base with citation support
**Duration**: 1-2 days
**Prerequisites**: Python 3.9+, AWS credentials, understanding of Pydantic models
**Success Criteria**: Agent can search feedback data with <2s response time and return structured citations

**Implementation Status**: 🚧 In Progress
- [x] Task 1: Set up Bedrock client integration
- [ ] Task 2: Implement search with business function filtering
- [ ] Task 3: Add citation extraction and formatting

**Implementation Observations**:
1. **Retry Logic Pattern**: Exponential backoff with jitter prevents thundering herd
2. **Citation Text Extraction**: Added 100-char snippets for better context
3. **Business Function Mapping**: Audit role gets access to all data sources

## Project Context
### Business Context
Enable users to search across customer feedback data sources with semantic understanding, supporting business function filtering and source traceability for compliance requirements.

**End Users**: Business analysts, compliance officers, call center managers
**Business Value**: Faster insight discovery, regulatory compliance, audit trail

### Technical Context
**Architecture Overview**:
```
User Query → Agent → Bedrock Knowledge Base → Search Results
                ↓
         Citation Extraction → Structured Response
```

**Integration Points**:
- Input: User queries with business function context
- Output: Search results with SourceCitation objects
- Dependencies: AWS Bedrock Knowledge Base, centralized session factory

**Performance Requirements**: <2s response time, <5MB memory usage

### Required Reading
**Essential Documents** (Read in this order):
1. **`planning/design/detailed-design.md`** - Focus on search architecture (lines 45-80)
   - Key takeaway: Business function filtering requirements

2. **`agent/lib/tools/feedback_tools.py`** - Current tool patterns
   - Key takeaway: Session factory usage and error handling

## Data Structure Understanding
### Input Data Format
```python
# User query with business context
{
    "query": "mobile app issues",
    "business_function": "digital_banking",
    "max_results": 20
}
```

### Output Data Format
```python
# Search response with citations
{
    "results": [...],
    "citations": [SourceCitation(...)],
    "query": "mobile app issues",
    "business_function": "digital_banking"
}
```

## Implementation Tasks
### Task 1: Set up Bedrock client integration
**Objective**: Create robust Bedrock Agent Runtime client with proper error handling

**What to Implement:**
- [ ] Bedrock client using centralized session factory
- [ ] Connection validation and health checks
- [ ] Retry logic with exponential backoff

**Key Requirements:**
- Use existing AWS session factory pattern
- Handle network timeouts and service errors
- Log all API interactions for debugging
- Response time <2 seconds

**Implementation Location**: `agent/lib/tools/feedback_tools.py`

**Code to Add**:
```python
@retry(exponential_backoff_with_jitter)
def _call_bedrock_knowledge_base(self, query: str) -> Dict[str, Any]:
    """Call Bedrock with retry logic and error handling."""
    response = self.bedrock_client.retrieve_and_generate(
        input={'text': query},
        retrieveAndGenerateConfiguration={
            'type': 'KNOWLEDGE_BASE',
            'knowledgeBaseConfiguration': {
                'knowledgeBaseId': self.knowledge_base_id
            }
        }
    )
    return response
```

**Acceptance Criteria:**
- [ ] Client successfully connects to Knowledge Base
- [ ] Retry logic handles transient failures (3 retries max)
- [ ] All errors logged with correlation IDs
- [ ] Response time <2 seconds for typical queries

**Test Strategy**:
- Unit tests: Mock Bedrock responses, test retry scenarios
- Integration tests: Real Knowledge Base calls with test data
- Performance tests: Validate <2s response time requirement
```

This example demonstrates the comprehensive structure with implementation status tracking, concrete code examples, specific performance requirements, and detailed technical context.

## Quality Checklist for Implementation Guides

### Content Completeness
- [ ] All context information included
- [ ] Clear objectives and scope defined
- [ ] Tasks broken down to 2-4 hour chunks
- [ ] Acceptance criteria are testable
- [ ] Best practices and standards included
- [ ] Handoff plan complete

### Developer Experience
- [ ] Can be followed by someone with no project knowledge
- [ ] All required information is self-contained
- [ ] Examples are concrete and executable
- [ ] Troubleshooting guidance included
- [ ] Clear success criteria defined

### Technical Quality
- [ ] Architecture decisions explained
- [ ] Integration points clearly defined
- [ ] Performance requirements specified
- [ ] Security considerations addressed
- [ ] Error handling requirements clear

### Process Integration
- [ ] Fits with overall development methodology
- [ ] Supports continuous integration/deployment
- [ ] Enables effective code review
- [ ] Facilitates knowledge transfer
- [ ] Supports project tracking and reporting

## Common Pitfalls to Avoid

### Scope Creep
- **Problem**: Tasks become too large or complex
- **Solution**: Keep tasks focused on single objectives
- **Check**: Each task should take 2-4 hours maximum

### Missing Context
- **Problem**: Developers can't understand the bigger picture
- **Solution**: Always include business context and architecture overview
- **Check**: Someone new to the project can follow the guide

### Vague Acceptance Criteria
- **Problem**: Unclear when work is "done"
- **Solution**: Make criteria binary and testable
- **Check**: Each criterion can be verified objectively

### Missing Integration Points
- **Problem**: Work doesn't connect properly with other components
- **Solution**: Explicitly define all dependencies and interfaces
- **Check**: Integration requirements are testable

## Measuring Success

A good implementation guide should result in:
- **Consistent Output**: Different developers produce similar quality results
- **Reduced Questions**: Minimal clarification needed during implementation
- **Faster Onboarding**: New developers can contribute quickly
- **Quality Deliverables**: Code meets standards without extensive rework
- **Smooth Handoffs**: Work integrates cleanly with other components

Use these metrics to continuously improve your implementation guide process.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
