---
name: implementation-planning
description: Create structured implementation plans with task breakdowns, change proposals, and verification steps. Use when starting new features, fixing bugs, refactoring code, or when user mentions "implementation plan", "design document", "technical spec", or needs to break down complex work into actionable tasks. Use when this capability is needed.
metadata:
  author: ziltorian
---

# Implementation Planning

Systematically research codebases, design solutions, and create actionable implementation plans before writing code. This skill teaches AI agents to separate planning from execution, ensuring thorough context gathering and clear task definition.

## Core Workflow

The implementation planning workflow follows a two-phase approach:

### Phase 1: Planning Mode

**Focus**: Understanding the problem and designing the solution

1. **Context Gathering** - Research the codebase comprehensively
2. **Plan Creation** - Document technical designs and task lists
3. **User Review** - Present plan for feedback and refinement
4. **Iteration** - Refine based on feedback until approved

### Phase 2: Implementation Mode

**Focus**: Executing the approved plan

1. **Task Execution** - Implement changes following the plan
2. **Progress Tracking** - Update task status in real-time
3. **Verification** - Test and validate changes
4. **Documentation** - Record what was done

## When to Use This Skill

**Planning Mode triggers:**
- Starting a new feature or user request
- Bug fixes requiring investigation
- Code refactoring or architecture changes
- Unexpected complexity discovered during execution

**Implementation Mode triggers:**
- Approved implementation plan exists
- User says "start implementation" or "begin work"
- Following up on a reviewed plan

## Planning Mode: Detailed Workflow

### Step 1: Context Gathering

Before writing any plan, gather comprehensive context using read-only operations:

**Start with high-level overview:**
1. Read project instructions in `.github/instructions/` related to the task
2. Identify main components and modules involved
3. Search for similar existing patterns in the codebase

**Progressive refinement:**
1. Use broad searches before reading specific files
2. Trace dependencies and related functionality
3. Identify edge cases and constraints

**Stopping criteria:**
- Reach 80% confidence you understand the problem
- Can articulate root causes and proposed changes
- Understand testing and verification requirements

**Tools for research:**
- `read_file` - Read project documentation and source files
- `file_search` - Find relevant files by pattern or content
- `grep` - Search for specific code patterns
- `list_directory` - Understand project structure

### Step 2: Create Implementation Instructions File

Create a structured markdown document that serves as the contract between planning and implementation phases.

**File naming:**
```
.github/implementations/implementation.instructions.md
.github/implementations/[module_name]_implementation.instructions.md
```

**File location:**
Store all implementation plans in `.github/implementations/` directory for consistency.

**YAML Frontmatter Structure:**

```yaml
---
name: Module_Name Implementation
description: Brief description of what this implementation achieves
applyTo: "src/module_name/**"
---
```

**Field specifications:**
- `name` - Descriptive title of the implementation
- `description` - 1-2 sentence summary of the implementation
- `applyTo` - Glob pattern specifying which files this plan applies to (avoid `**` for global, use specific paths)

See `references/YAML_FRONTMATTER.md` for detailed frontmatter examples and validation rules.

### Step 3: Document Structure

The implementation instructions file must contain these sections:

#### Section 1: Goal

Clear, focused description of what will be achieved.

**Structure:**
```markdown
## Goal
[1-2 sentence summary of outcome]

## Background / Context
[Problem description - concise, factual]

**Observed symptoms:**
- [Specific symptom 1]
- [Specific symptom 2]

**Root cause (hypothesis):**
- [Identified cause 1]
- [Identified cause 2]
```

**Rules:**
- Goal must be 1-2 sentences maximum
- Separate symptoms from root causes
- Use bullet points, not paragraphs
- Be factual and specific

**Example - Bad:**
```markdown
## Goal
Fix several related defects: The pagination is broken because sometimes the code thinks there are no more pages even though there are. Also the logging shows weird messages...
```

**Example - Good:**
```markdown
## Goal
Fix pagination detection and improve authentication stability.

## Background

**Problem 1: Pagination**
- Symptom: Log shows "Page 14/30" then "Last page reached" (actual pages: 14)
- Cause: `hasNextPage()` relies on single selector that may be disabled

**Problem 2: Authentication**
- Symptom: `TimeoutException` when searching for login field
- Cause: Website changed redirect URL and form selectors
```

#### Section 2: Proposed Changes

Detail WHAT will change (not HOW to implement).

**Structure:**
```markdown
## Proposed Changes

### [Component Name]

#### [MODIFY] `filename.py`
**Method/function:** `method_name` (lines X-Y)
**Changes:**
- [Specific change 1]
- [Specific change 2]

#### [NEW] `new_file.py`
**Purpose:** [What the file does]
**Contains:**
- `ClassName` - [brief description]
- `function_name()` - [brief description]
```

**Rules:**
- Group files by logical components
- Use `[MODIFY]`, `[NEW]`, `[DELETE]` markers
- Specify method/function names and line numbers
- Describe changes, not implementation code
- No code snippets in this section

**Example - Bad:**
```markdown
### [MODIFY] src/utils.py
- Function: `def validate_input(self) -> bool` (lines around current).
- Changes: Check multiple conditions like `input.length > 0`, `input.type == 'string'`
  Ignore empty values and throw exceptions...
```

**Example - Good:**
```markdown
### Validation Module

#### [MODIFY] `utils.py`
**Method:** `validate_input()` (line 45)
- Add multiple validation rules
- Check for empty and null values
- Return detailed error messages

**Method:** `ValidationResult` [NEW]
- Dataclass for structured validation responses
- Contains `is_valid`, `errors`, `warnings` fields
```

#### Section 3: Task List

Break down implementation into atomic, actionable tasks.

**Structure:**
```markdown
## Task

### [Component 1]
- [ ] Task description <!-- id: C1-1 -->
- [ ] Task description <!-- id: C1-2 -->

### [Component 2]
- [ ] Task description <!-- id: C2-1 -->
```

**Rules:**
- Group tasks by component (matching Proposed Changes)
- Each task must be atomic (single action)
- Use unique IDs in HTML comments
- **NEVER DUPLICATE** tasks
- Tasks correspond to Proposed Changes items

**Task status markers:**
- `[ ]` - Not started
- `[/]` - In progress
- `[x]` - Completed

**Example - Bad:**
```markdown
## Task
- [ ] Implement improved validation with multiple rules. <!-- id: V-2 -->
- [ ] Write tests for validation and API. <!-- id: T-1 -->
- [ ] Write tests for validation and API. <!-- id: T-1 --> <!-- DUPLICATE! -->
```

**Example - Good:**
```markdown
## Task

### Validation
- [ ] Add multiple validation rules to `validate_input()` <!-- id: V-1 -->
- [ ] Create `ValidationResult` dataclass <!-- id: V-2 -->
- [ ] Update error handling in validators <!-- id: V-3 -->

### API
- [ ] Add input validation to POST endpoints <!-- id: A-1 -->
- [ ] Return structured error responses <!-- id: A-2 -->

### Tests
- [ ] Create `test_validation.py` with unit tests <!-- id: T-1 -->
- [ ] Create `test_api_errors.py` for integration tests <!-- id: T-2 -->
```

#### Section 4: User Review Required (Optional)

Use ONLY for breaking changes or critical decisions requiring user input.

**Structure:**
```markdown
## User Review Required

> [!IMPORTANT]
> [Specific question requiring user's decision]

> [!WARNING]
> [Warning about potential risks or impacts]
```

**Rules:**
- Use alerts only for truly important questions
- Formulate as specific questions, not information
- Omit this section if no critical decisions needed

#### Section 5: Verification (Optional)

Define how to validate the implementation.

**Structure:**
```markdown
## Verification

**Unit tests:**
- `pytest tests/test_*.py -v`
- `npm test -- --coverage`

**Manual verification:**
1. [Specific step to verify]
2. [Expected outcome]
3. [Edge case to test]

**Integration tests:**
- [End-to-end test scenarios]
```

### Step 4: File Requirements and Constraints

**Maximum file size:** 12,000 characters

**Formatting requirements:**
- Use clear markdown headers
- Each section must be clearly labeled
- Use bullet points for readability
- **NO code blocks** - only method/class names
- Check for duplicate tasks before finalizing

**Content restrictions:**
- No concrete implementation code
- No long explanations (use bullet points)
- Reference libraries and method names only
- Focus on WHAT changes, not HOW to code

### Step 5: Present Plan for Review

After creating the implementation instructions file:

1. **Present to user** - Frame as draft for feedback
2. **Request review** - Ask for approval or changes
3. **Iterate on feedback** - Refine plan based on input
4. **DO NOT start implementation** - Stay in planning mode until explicitly approved

## Implementation Mode: Execution Workflow

Once an implementation plan is approved, switch to implementation mode to execute the tasks.

### Step 1: Locate Implementation Plan

**If plan is in context:**
- Use the provided `*_implementation.instructions.md` file

**If plan is not in context:**
1. Search for `**/*implementation.instructions.md` pattern
2. Typically located in `.github/implementations/` directory
3. Select most appropriate plan based on task context
4. If multiple plans exist, process all relevant ones
5. If no plan exists, continue with user's requested tasks

### Step 2: Execute Tasks Sequentially

Follow the task list from the implementation instructions file:

**For each task:**
1. Read the task description and requirements
2. Implement the changes in source files
3. Update task status in implementation file:
   - Mark `[/]` when starting
   - Mark `[x]` when complete
4. Handle blockers:
   - If issue encountered, mark task `[/]`
   - Investigate the project to understand problem
   - Add findings to implementation file
   - Report progress to user

**When adding new functionality:**
- Check if tests exist for related code
- If no tests exist, add task to create tests
- Implement tests in `tests/` directory
- Follow project testing conventions

### Step 3: Report Progress

After completing changes:

**Update implementation file:**
1. Mark all completed tasks with `[x]`
2. Add "Changes Made" section listing:
   - Files modified
   - Key changes in each file
   - New files created

**Run verification:**
1. Execute basic tests or checks
2. Record results in "Verification" section
3. Note any failures or issues

**Add deployment notes (if applicable):**
- Configuration changes needed
- Migration steps required
- Environment variable updates
- Deployment instructions

**Example "Changes Made" section:**
```markdown
## Changes Made

### Validation Module (`src/utils.py`)
- Added `ValidationResult` dataclass (lines 10-15)
- Refactored `validate_input()` with multiple rules (lines 45-78)
- Added detailed error messages for each validation type

### API Layer (`src/api/handlers.py`)
- Integrated new validation in POST endpoints (lines 123-145)
- Updated error response format to use ValidationResult

### Tests
- Created `tests/test_validation.py` with 12 unit tests
- Created `tests/test_api_errors.py` with 5 integration tests
- All tests passing
```

## Stopping Rules

**In Planning Mode:**
- NEVER write production code
- ONLY create/edit files in `.github/implementations/`
- STOP if considering implementation or code changes
- Use only: `createDirectory`, `createFile`, `editFiles`, `readFile` for implementation docs

**In Implementation Mode:**
- Follow the approved plan strictly
- Don't deviate without updating the plan first
- Report blockers before making significant changes
- Always update task status in real-time

## Best Practices

### Planning Phase

**Research thoroughly:**
- Invest time in context gathering (80% confidence rule)
- Don't rush to create plan without understanding
- Read project instructions and conventions first

**Keep plans focused:**
- Break large features into multiple plans
- One plan per module or logical component
- Use `applyTo` glob patterns to scope each plan

**Make plans actionable:**
- Tasks must be atomic and clear
- Include enough detail to execute independently
- Avoid vague tasks like "improve code quality"

**Communicate clearly:**
- Use bullet points over paragraphs
- Separate symptoms from causes
- Be specific with file names and line numbers

### Implementation Phase

**Follow the plan:**
- Execute tasks in order
- Update status after each task
- Don't skip verification steps

**Handle uncertainty:**
- Flag blockers immediately
- Investigate before making assumptions
- Update plan if requirements change

**Test comprehensively:**
- Add tests for new functionality
- Run existing tests after changes
- Document test results in verification section

## Common Pitfalls to Avoid

❌ **Starting implementation without plan**
✅ Create plan first, get approval, then implement

❌ **Mixing symptoms and causes in Goal section**
✅ Separate into "Observed symptoms" and "Root cause" subsections

❌ **Writing code snippets in Proposed Changes**
✅ Describe what changes, not how to implement

❌ **Duplicate tasks in task list**
✅ Each task appears once with unique ID

❌ **Vague tasks like "improve validation"**
✅ Specific tasks like "Add null check to validate_input()"

❌ **Skipping verification section**
✅ Always define how to verify the implementation

❌ **Files over 12,000 characters**
✅ Keep implementation instructions concise and focused

## Integration with VS Code

This skill is designed for custom agents in VS Code with GitHub Copilot. The workflow aligns with:

- **Custom Instructions**: Planning and implementation prompts use `.instructions.md` format
- **YAML Frontmatter**: Following VS Code custom agent specification
- **File Organization**: Using `.github/` directory conventions
- **Agent Handoffs**: Separating planning and implementation agents

See `references/VS_CODE_INTEGRATION.md` for detailed VS Code setup instructions.

## References

For additional details and examples:

- `references/YAML_FRONTMATTER.md` - Complete frontmatter specification and examples
- `references/VS_CODE_INTEGRATION.md` - VS Code custom agent setup
- `references/EXAMPLES.md` - Real-world implementation plan examples
- `references/TASK_TRACKING.md` - Advanced task management patterns

## Summary

**Planning Mode** = Research → Design → Document → Review → Iterate

**Implementation Mode** = Locate Plan → Execute Tasks → Update Status → Verify → Report

This separation ensures thorough design before coding and systematic execution with clear progress tracking.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ziltorian) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
