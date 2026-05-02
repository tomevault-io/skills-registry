---
name: task-structure
description: Task structure template and guidelines for tracking implementation work in tasks.md. Use when creating or updating tasks Use when this capability is needed.
metadata:
  author: lovelystack
---

# Task Structure

This skill defines the standard structure for all implementation tasks in `docs/tasks.md`.

## Supported Task Formats

The lovely-tasks plugin supports four task formats:

1. **Detailed Format** (this document) - Comprehensive task tracking with status, acceptance criteria, tests, and documentation
2. **Spec-Kit Format** - Simple checklist with T-prefixed task IDs (e.g., `- [ ] T001 Task description`)
3. **OpenSpec Format** - Hierarchical task structure with numbered tasks (e.g., `- [ ] 1.1 Task description`)
4. **Generic Checklist** - Any markdown file with checkboxes for simple task tracking

This skill focuses on the **Detailed Format**. For other formats, see the [Hooks Documentation](../../docs/hooks.md).

## Task Status Values

Tasks must have one of these status values:

- **TODO** - One or more Acceptance Criteria are incomplete and you are not actively working on it
- **In Progress** - One or more Acceptance Criteria are incomplete and you ARE actively working on it
- **Complete** - ALL Acceptance Criteria are complete (all checkboxes checked)

**Critical Rule**: A task can ONLY be marked Complete if every single acceptance criteria, test, and documentation checkbox is marked [x]. No exceptions.

## Task Template Structure

Every task must follow this structure:

### Header

```markdown
### Task X.Y: Task Name

**Status**: TODO | In Progress | Complete

**Description**: Brief description of what needs to be done.
Contains references to code, documentation, and other artifacts.
```

### Research Required (optional)

```markdown
**Research Required**:

- [ ] Checklist of research items needed before implementation
- [ ] Each item should be marked complete when done
- [ ] Results must be added to the notes section
```

### Implementation

```markdown
**Implementation**:

- Draft plan of the implementation
- Recommended way of breaking down the work
- Not necessarily the way things must be implemented
- If there are gaps or uncertainties, they should be noted in notes
- Any implementation details or deviations should be noted here
```

### Acceptance Criteria (required)

```markdown
**Acceptance Criteria**:

- [ ] Checklist of specific measurable outcomes
- [ ] Must be met for task to be considered complete
- [ ] Each item marked complete only when done AND tested
- [ ] Every box must be [x] for status to be Complete
```

### Demo (optional)

```markdown
**Demo**:

- [ ] This section must be included only if we can validate task visually, for example UI changes
- [ ] Create demo folder if it does not exist - {TASK_FILE_DIRECTORY}/demo/{TASK NAME}
- [ ] Describe step by step process for creating the demo, for example with Playwright MCP server if it's a web UI task
- [ ] Make sure to include instructions to verify each acceptance criteria, ensure that there are no crashes or errors in application durihg demo
```

### Tests (required)

```markdown
**Tests**:

- [ ] Checklist of specific tests that must be created or updated
- [ ] Tests must pass before marking task complete
```

### Documentation (required)

```markdown
**Documentation**:

- [ ] Specific documentation that must be created or updated
- [ ] Each item marked complete only when done
- [ ] Extra changes should be noted in notes
```

### Notes (required)

```markdown
**Notes**:

- Keep track of progress and findings
- Keep track of solutions tried (workingsolutions or not working solutions)
- Document deviations from implementation plan
- Record blockers and dependencies
```

## Guidelines

### When Creating a Task

1. **Be Specific**: Acceptance criteria must be measurable and testable
2. **Be Complete**: Include all sections (Description, Implementation, AC, Tests, Docs, Notes)
3. **Be Realistic**: Don't mark something Complete if ANY checkbox is unchecked
4. **Reference Files**: Include specific file paths and line numbers where relevant

### When Updating a Task

1. **Check Boxes as You Go**: Mark [x] immediately when work is done
2. **Update Notes**: Document progress, issues, solutions tried
3. **Change Status**:
   - TODO → In Progress when you start work
   - In Progress → Complete ONLY when ALL boxes are [x]
4. **Document Deviations**: If implementation differs from plan, note it

### Common Mistakes to Avoid

❌ **Don't**: Mark task Complete with unchecked boxes
✅ **Do**: Keep status as In Progress or TODO until ALL criteria met

❌ **Don't**: Leave Notes section empty
✅ **Do**: Document what was done, what worked, what didn't

❌ **Don't**: Skip Tests or Documentation sections
✅ **Do**: Include test coverage and doc updates for every task

❌ **Don't**: Make acceptance criteria vague ("make it work")
✅ **Do**: Make them specific and measurable ("all 5 unit tests pass")

## Example Task

```markdown
### Task 3.9: Implement Request Debouncing

**Status**: In Progress

**Description**: Add debouncing to analysis requests to prevent overwhelming the LLM with rapid-fire requests when user is actively typing.

**Implementation**:

1. In `src-tauri/src/core/service.rs`:
   - Add 500ms debounce timer
   - Cancel pending requests on new input
   - Use tokio::time::sleep for delay
2. Emit cancellation events to UI

**Acceptance Criteria**:

- [x] Debounce timer implemented with 500ms delay
- [x] Pending requests cancelled on new input
- [ ] UI receives cancellation events
- [ ] No memory leaks from cancelled requests
- [ ] Pre-commit checks pass

**Demo**:

- [ ] Create a demo folder in `./tasks/demo/Task 3.9: Implement Request Debouncing/
- [ ] Use Playwright MCP server to simulate rapid typing
- [ ] Make a screenshot of the screen during rapid typing
- [ ] Make a screenshot of the screen after typing stops and request is processed
- [ ] Make a vidoe showing that requests are debounced and cancellations occur without errors

**Tests**:

- [x] Unit test: `test_debounce_delay()`
- [ ] Unit test: `test_cancellation_on_new_input()`
- [ ] Integration test: `test_rapid_typing_scenario()`

**Documentation**:

- [ ] Update architecture.md with debouncing flow
- [ ] Add debouncing to performance.md

**Notes**:

- Implemented basic debouncing with tokio::time::sleep
- Cancellation works but events not yet wired to UI
- Found edge case: rapid focus changes need handling
- TODO: Wire cancellation events to emit_widget_state_changed()
- Blocker: Waiting on Task 4.3 (Event System) completion
```

## Integration with docs/tasks.md

All tasks are tracked in `docs/tasks.md`. When working on tasks:

1. **Find Your Task**: Search for the task number (e.g., "Task 2.4")
2. **Update Status**: Change from TODO → In Progress when starting
3. **Check Boxes**: Mark [x] as you complete each item
4. **Update Notes**: Document your progress continuously
5. **Mark Complete**: Only when ALL boxes are [x]

## References

- Full task list: `docs/tasks.md`
- Product requirements: `docs/product-spec.md`
- Technical specification: `docs/tech-spec.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lovelystack) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
