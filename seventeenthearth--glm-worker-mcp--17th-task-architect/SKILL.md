---
name: 17th-task-architect
description: | Use when this capability is needed.
metadata:
  author: seventeenthearth
---

# Task Architect

Orchestrate GPT-5.3-Codex xhigh and GLM-5 to create comprehensive TASK specifications.

## When to Use

- Starting a new TASK implementation
- Converting roadmap TASK entries into actionable execution plans
- Creating design documents for complex feature implementations

## Workflow Overview

```
Phase 0: Workspace Setup
    └── Rotate existing .kkachi/{branch} to .kkachi/{branch}-trial-{n}
    └── Create fresh .kkachi/{branch}/ directory

Phase 1: Requirements Elicitation (GPT-5.3-Codex xhigh)
    └── Analyze what code/documentation info is needed
    └── Generate search query list (max 15, only what's necessary)

Phase 2: Parallel Research (GLM-5 x N) [BLOCKING + STREAMING]
    └── submit_task x N concurrent calls
    └── wait_for_completion(task_ids) with streaming progress
    └── Progress auto-reported: started/completed/rate-limited
    └── Save reports to .kkachi/{branch}/completed/*.md

Phase 2.5: Report Verification (Subagent x N) [PARALLEL]
    └── Each report reviewed by a subagent
    └── Spot-check claims against actual codebase
    └── Append "Reviewer's Note" section to each report
    └── Flag issues for GPT-5.3-Codex attention

Phase 3: Synthesis + Draft (GPT-5.3-Codex xhigh, same thread)
    └── Aggregate reports (pay extra attention to flagged sections)
    └── Cross-verify flagged issues
    └── Create plan draft
    └── Generate list of decisions needed

Phase 4: Interview Loop (GPT-5.3-Codex xhigh, same thread)
    └── Ask user about pending decisions
    └── Handle re-explanations and detail requests
    └── Repeat until all decisions are finalized

Phase 5: Draft Plan (GPT-5.3-Codex xhigh, same thread)
    └── Record only finalized decisions (no "recommended/alternative/optional")
    └── Include checklist
    └── Save draft to TASK.md

Phase 6: Approval Loop (GPT-5.3-Codex xhigh, same thread)
    └── Present TASK.md for user review
    └── Answer user questions about the plan
    └── Make requested changes
    └── Repeat until user gives final approval
    └── Only complete when user explicitly approves
```

## Critical Rules

### No Report Reading by Main Agent

**NEVER use Read tool to read GLM reports.** Pass file paths to GPT-5.3-Codex and let it read directly.

- Main agent: orchestration only, passes file paths
- GPT-5.3-Codex: reads files, analyzes, synthesizes

This saves ~90KB of context per run.

### Single Thread

GPT-5.3-Codex xhigh must maintain **the same thread from start to finish**.

```
codex() → acquire threadId
    ↓
codex-reply(threadId) → Phase 3 (synthesis)
    ↓
codex-reply(threadId) → Phase 4 (interview, repeat)
    ↓
codex-reply(threadId) → Phase 5 (draft TASK.md)
    ↓
codex-reply(threadId) → Phase 6 (approval loop, repeat until approved)
```

### Search Efficiency

- Maximum 15 parallel searches allowed
- But request **only what's needed** (5-10 is typical)
- GPT-5.3-Codex xhigh must read and synthesize all reports
- Unnecessary searches only add noise

### Interview Completeness

- Repeat interviews **until plan is complete**
- Re-explain if user doesn't understand the question
- Provide additional context when detailed info is requested

### Interview UX

- **Always use `AskUserQuestion` tool** for presenting choices
- GPT-5.3-Codex returns structured questions → Claude converts to AskUserQuestion
- User navigates with ↑↓ keys and selects with Enter
- "Other..." option allows custom text input
- Never dump raw text questions - always use the interactive UI

### Output Quality

TASK.md must satisfy these criteria:
- No words like "recommended", "alternative", "optional"
- Only finalized decisions recorded
- **Executable by mid-level developer who is NEW to this project**

**Detail Level Required:**

A mid-level developer joining this project for the first time should be able to complete the task using ONLY this document. Include:

| Section | Required Content |
|---------|------------------|
| File paths | Exact paths: `internal/usecase/space.go`, not "the usecase layer" |
| Function signatures | Full signatures: `func (u *SpaceUseCase) Create(ctx context.Context, req *CreateSpaceRequest) (*Space, error)` |
| Dependencies | What to import, what to inject |
| Implementation steps | Numbered, sequential, unambiguous |
| Code examples | Skeleton code or pseudocode for complex logic |
| Error handling | Which errors to return, how to wrap them |
| Validation | What to validate, what error messages |
| Tests | Which test cases to write, expected behaviors |
| Edge cases | Explicitly list edge cases to handle |

**Bad Example:**
```
- Add Create method to SpaceUseCase
- Handle errors appropriately
- Add tests
```

**Good Example:**
```
1. Create file: internal/usecase/space_usecase.go

2. Add SpaceUseCase struct:
   type SpaceUseCase struct {
       repo domain.SpaceRepository
       logger *slog.Logger
   }

3. Add Create method:
   func (u *SpaceUseCase) Create(ctx context.Context, req *CreateSpaceRequest) (*Space, error)

   Implementation:
   a. Validate req.Name is not empty → return ErrInvalidName
   b. Check repo.ExistsByName(ctx, req.Name) → return ErrAlreadyExists if true
   c. Create domain.Space{ID: uuid.New(), Name: req.Name, CreatedAt: time.Now()}
   d. Call repo.Save(ctx, space)
   e. Return space, nil

4. Error definitions (add to internal/domain/errors.go):
   var ErrInvalidName = errors.New("space name cannot be empty")
   var ErrAlreadyExists = errors.New("space already exists")

5. Test cases (internal/usecase/space_usecase_test.go):
   - TestCreate_Success: valid input → returns space
   - TestCreate_EmptyName: empty name → returns ErrInvalidName
   - TestCreate_Duplicate: existing name → returns ErrAlreadyExists
```

**TODO Checklist (Required):**

Every TASK.md MUST end with a TODO checklist. Opus uses this to track progress.

```markdown
## Checklist

- [ ] Create `internal/usecase/space_usecase.go`
- [ ] Implement `SpaceUseCase` struct with dependencies
- [ ] Implement `Create` method with validation
- [ ] Add error definitions to `internal/domain/errors.go`
- [ ] Create `internal/usecase/space_usecase_test.go`
- [ ] Test: `TestCreate_Success`
- [ ] Test: `TestCreate_EmptyName`
- [ ] Test: `TestCreate_Duplicate`
- [ ] Run `make test` - all tests pass
- [ ] Run `make lint` - no lint errors
```

Checklist rules:
- One item per concrete action
- Include file paths in each item
- Include test execution at the end
- Opus checks off items as completed

**Why This Matters:**
- Opus implements based on TASK.md
- Vague instructions → Opus guesses → wrong implementation
- Detailed instructions → Opus follows → correct implementation

### User Approval Required

**The workflow is NOT complete until the user explicitly approves.**

- Always present TASK.md for user review after drafting
- Answer all user questions about the plan
- Make all requested changes
- Only finish when user says "approved" (or equivalent)
- Never skip the approval loop

## Implementation

### Phase 0: Workspace Setup

```python
import os
from pathlib import Path

def setup_workspace(project_root: str) -> str:
    """
    Prepare .kkachi workspace with trial rotation.

    If .kkachi/{branch}/ exists, rename to .kkachi/{branch}-trial-{n}/
    where n increments from 1.

    Returns: output_dir path
    """
    # Get current branch name
    branch = subprocess.check_output(
        ["git", "rev-parse", "--abbrev-ref", "HEAD"],
        cwd=project_root,
        text=True
    ).strip()

    kkachi_root = Path(project_root) / ".kkachi"
    current_dir = kkachi_root / branch

    # Rotate existing directory if present
    if current_dir.exists():
        # Find next trial number
        trial_num = 1
        while (kkachi_root / f"{branch}-trial-{trial_num}").exists():
            trial_num += 1

        # Rename current to trial-N
        trial_dir = kkachi_root / f"{branch}-trial-{trial_num}"
        current_dir.rename(trial_dir)
        log(f"Rotated existing workspace to {trial_dir}")

    # Create fresh workspace
    output_dir = current_dir / "completed"
    output_dir.mkdir(parents=True, exist_ok=True)

    log(f"Created workspace: {output_dir}")
    return str(output_dir)
```

### Phase 1: Start Codex Session

```python
# Start GPT-5.3-Codex xhigh session
response = codex(
    model="gpt-5.3-codex",
    config={"model_reasoning_effort": "xhigh"},
    prompt="""
You are an expert in Go, gRPC, and Clean Architecture.

TASK: {task_id}
Roadmap content: {roadmap_content}
Related docs: {related_docs}

Analyze what codebase information is needed to design this TASK.
Return search query list in JSON format:

{
  "queries": [
    {
      "id": "01",
      "topic": "space proto structure",
      "search_instruction": "rg patterns and paths to search",
      "output_file": "space-proto-analysis.md"
    },
    ...
  ]
}

Note:
- Request only truly necessary searches (max 15)
- You will read and synthesize all reports yourself
- Unnecessary searches only add noise
""",
    sandbox="workspace-write",
    approval-policy="never"
)

threadId = response.threadId
queries = parse_json(response.output)
```

### Phase 2: Parallel GLM Search (Non-blocking)

```python
# Setup workspace with trial rotation
output_dir = setup_workspace(project_root)

# Submit all GLM tasks (non-blocking)
task_ids = []
for query in queries:
    result = submit_task(
        task=f"""
{query['search_instruction']}

Save results to:
{output_dir}/{query['output_file']}

Report format:
# {query['topic']}

## Search Results
- file:line list
- relevant code snippets

## Analysis
- discovered patterns
- key implementation locations
- related dependencies
""",
        working_dir=project_root,
        max_iterations=100
    )
    # submit_task returns: "Task submitted: abc12345\n\nUse get_task_status..."
    task_id = result.split(": ")[1].split("\n")[0]
    task_ids.append(task_id)

# Wait for all tasks with streaming progress
# Progress auto-reported via MCP Context:
#   🚀 [abc123] Started
#   ⚠️ [def456] Rate limited (retry 2/10), waiting 1 min...
#   ✅ [abc123] Completed
#   🏁 Done: 8 completed
result = wait_for_completion(task_ids, timeout=1800)

print(f"✅ GLM research complete: {result['completed']} completed, {result['failed']} failed")
```

### Phase 2.5: Report Verification (Parallel Subagents)

After GLM reports are complete, spawn subagents to verify each report:

```python
# GLM tasks completed (wait_for_completion returned)

# Get list of completed reports
report_files = glob.glob(f"{output_dir}/*.md")

# Spawn verification subagents in parallel (use haiku for speed)
for report_file in report_files:
    Task(
        subagent_type="oh-my-claudecode:explore",  # Fast, read-only
        model="haiku",
        prompt=f"""
Review this GLM research report and verify its claims against the actual codebase.

Report file: {report_file}

## Verification Checklist

1. **Existence Checks**
   - Do mentioned files actually exist?
   - Are functions/classes at the stated locations?
   - Are line numbers accurate?

2. **Content Verification**
   - Do code snippets match what's actually in the files?
   - Are described relationships accurate (e.g., "A calls B")?
   - Are identified patterns real patterns (not one-off)?

3. **Logical Consistency**
   - Does the analysis match the actual code?
   - Any contradictions in the report?
   - Any important aspects missed?

4. **Completeness**
   - Are there related files that should have been mentioned?
   - Any obvious gaps in coverage?

## Output

Append a "Reviewer's Note" section to the end of the report file:

```markdown
---
## Reviewer's Note

### Verified ✓
- [List items that were verified as correct]

### Issues Found ⚠️
- [List any inaccuracies with corrections]
- [Format: "Report claims X but actual is Y"]

### Missing Coverage
- [List any related files/code that should have been mentioned]
```

If everything checks out, write:
```markdown
---
## Reviewer's Note

### Verified ✓
All claims spot-checked and verified accurate.
```

Be concise. This is a spot-check, not a full audit.
"""
    )
```

### Phase 3: Synthesis

**IMPORTANT: Do NOT use Read tool to read reports. Pass file paths to GPT-5.3-Codex and let it read directly.**

This saves ~90KB of context that would otherwise be wasted on the main agent.

```python
# Wait for verification subagents to complete
# (They run in parallel and append Reviewer's Notes to reports)

# List report files (paths only, do NOT read contents)
report_files = glob.glob(f"{output_dir}/*.md")

# Synthesize reports in same thread - GPT reads files directly
response = codex_reply(
    threadId=threadId,
    prompt=f"""
GLM searches and verification are complete.

Report files to read and analyze:
{chr(10).join(f'- {f}' for f in report_files)}

**You must read each file yourself using your file reading capability.**
Each report has a "Reviewer's Note" section at the bottom.

Read each report and synthesize:
1. Pay EXTRA attention to sections with "Issues Found ⚠️"
2. Cross-verify flagged claims before relying on them
3. Note any "Missing Coverage" items that might need follow-up
4. Organize verified information for TASK design
5. Create plan draft
6. List decisions that need user input

IMPORTANT: If a Reviewer's Note flags an issue, verify it yourself
before including that information in the plan.

Output format:
## Verification Summary
- Reports with issues: [list]
- Critical issues resolved: [how]

## Plan Draft
(plan here)

## Decisions Needed
1. [Question 1]: Option A vs B
2. [Question 2]: ...
"""
)
```

### Phase 4: Interview Loop

GPT-5.3-Codex generates questions → Claude presents them using `AskUserQuestion` tool for better UX.

```python
while True:
    # GPT-5.3-Codex returns questions in structured format
    # Example response.questions:
    # [
    #   {
    #     "question": "Which authentication method should we use?",
    #     "header": "Auth method",
    #     "options": [
    #       {"label": "JWT tokens (Recommended)", "description": "Stateless, good for microservices"},
    #       {"label": "Session-based", "description": "Traditional, requires session store"},
    #       {"label": "OAuth2", "description": "Third-party integration"}
    #     ]
    #   }
    # ]

    # Claude converts to AskUserQuestion tool call
    for q in response.questions:
        answer = AskUserQuestion(
            questions=[{
                "question": q["question"],
                "header": q["header"],  # Short label (max 12 chars)
                "options": q["options"],
                "multiSelect": q.get("multiSelect", False)
            }]
        )
        user_responses.append(answer)

    # Forward all responses to GPT-5.3-Codex
    response = codex_reply(
        threadId=threadId,
        prompt=f"User responses: {json.dumps(user_responses)}"
    )

    # Check if complete
    if response.interview_complete:
        break
```

**Question Format from GPT-5.3-Codex:**

GPT-5.3-Codex should return questions in this JSON format:
```json
{
  "questions": [
    {
      "question": "Which database should we use for user data?",
      "header": "Database",
      "options": [
        {"label": "PostgreSQL (Recommended)", "description": "ACID compliant, good for relational data"},
        {"label": "MongoDB", "description": "Document store, flexible schema"},
        {"label": "SQLite", "description": "Embedded, good for small scale"}
      ],
      "multiSelect": false
    }
  ],
  "interview_complete": false
}
```

**UI Experience:**
```
┌─ Database ─────────────────────────────────────┐
│ Which database should we use for user data?    │
│                                                │
│ ● PostgreSQL (Recommended)                     │
│   ACID compliant, good for relational data     │
│                                                │
│ ○ MongoDB                                      │
│   Document store, flexible schema              │
│                                                │
│ ○ SQLite                                       │
│   Embedded, good for small scale               │
│                                                │
│ ○ Other...                                     │
└────────────────────────────────────────────────┘
```

User can:
- Use ↑↓ arrow keys to navigate
- Press Enter to select
- Choose "Other..." to type custom response

### Phase 5: Draft Plan

```python
response = codex_reply(
    threadId=threadId,
    prompt="""
All decisions are finalized.

Now create the TASK.md draft with EXTREME DETAIL.

**Target Reader:**
A mid-level developer who is NEW to this project and will implement
this task using ONLY this document. They have no prior context.

**Required Detail Level:**
- Exact file paths (e.g., `internal/usecase/space.go`)
- Full function signatures with all parameters and return types
- Step-by-step implementation instructions (numbered, sequential)
- Code skeletons or pseudocode for complex logic
- Error definitions and how to handle each error
- Validation rules and error messages
- Test cases with expected inputs and outputs
- Edge cases explicitly listed
- **TODO Checklist at the end** (one item per action, with file paths)

**Format:**
1. NO vague phrases like "handle errors appropriately" or "add tests"
2. NO words like "recommended", "alternative", "optional"
3. EVERY implementation step must be unambiguous
4. Include code examples where logic is non-trivial

**Remember:**
- Opus will implement based on this document
- Vague instructions → wrong implementation
- Detailed instructions → correct implementation

**File Writing:**
You have write access to the workspace. Save the draft to:
`.kkachi/{branch}/TASK-DRAFT.md`

IMPORTANT: Only write to `.kkachi/` directory. Do NOT write to other locations.

After saving, present the plan to the user for review.
Ask: "Please review the TASK draft at `.kkachi/{branch}/TASK-DRAFT.md`. You can:
- Ask questions about any part of the plan
- Request changes or additions
- Say 'approved' when you're satisfied"
"""
)
```

### Phase 6: Approval Loop

```python
# Loop until user explicitly approves
while True:
    user_input = get_user_input()

    # Check for approval
    if is_approval(user_input):  # "approved", "승인", "LGTM", etc.
        response = codex_reply(
            threadId=threadId,
            prompt=f"""
User has approved the TASK.md.

Finalize and confirm:
1. Ensure TASK.md is saved with all changes
2. Output a brief summary of what was planned
3. Confirm the workflow is complete

User message: {user_input}
"""
        )
        break  # Exit loop - workflow complete

# After approval: Claude moves draft to final TASK.md
# (GPT saved to .kkachi/{branch}/TASK-DRAFT.md, Claude moves it)
import shutil
draft_path = f".kkachi/{branch}/TASK-DRAFT.md"
final_path = "TASK.md"
shutil.move(draft_path, final_path)
print(f"✓ TASK.md created from approved draft")

    # Handle questions or change requests
    response = codex_reply(
        threadId=threadId,
        prompt=f"""
User has feedback on the TASK.md.

User message: {user_input}

Instructions:
1. If it's a QUESTION: Explain your reasoning clearly
2. If it's a CHANGE REQUEST:
   - Make the requested changes to TASK.md
   - Explain what you changed and why
3. After responding, ask if they have more feedback or are ready to approve

Always end with asking for approval or more feedback.
"""
    )

    # Continue loop for more feedback
```

**Approval Detection:**
```python
def is_approval(user_input: str) -> bool:
    """Detect if user is approving the plan."""
    approval_phrases = [
        "approved", "approve", "lgtm", "looks good",
        "승인", "확인", "좋아", "괜찮아", "완료",
        "go ahead", "ship it", "done", "ok", "okay"
    ]
    normalized = user_input.lower().strip()
    return any(phrase in normalized for phrase in approval_phrases)
```

**Example Interaction:**
```
GPT-5.3-Codex: "TASK.md draft is ready. Please review:
         [summary of key points]

         You can ask questions, request changes, or say 'approved'."

User: "왜 Repository 패턴 대신 직접 DB 접근으로 했어?"
GPT-5.3-Codex: "좋은 질문입니다. 이 경우에는... [설명]
         더 질문이나 수정 사항이 있으신가요?"

User: "이해했어. 근데 에러 핸들링 부분을 더 자세히 써줘."
GPT-5.3-Codex: "네, 에러 핸들링 섹션을 업데이트했습니다:
         - [변경 내용]
         TASK.md가 업데이트되었습니다. 다른 수정 사항이 있으신가요?"

User: "승인"
GPT-5.3-Codex: "TASK.md가 최종 확정되었습니다.

         Summary:
         - [주요 내용 요약]

         Task Architect workflow complete."
```

## Configuration

### Role Separation

| Agent | Role | Context Usage |
|-------|------|---------------|
| Main (Claude) | Orchestration, path passing, user interaction | Minimal |
| GLM-5 | Research, report writing | Own context |
| GPT-5.3 Codex | Report reading, analysis, design, TASK.md writing | Own context |

**Key principle**: Main agent never reads report contents. Only passes file paths.

### Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Codex model | gpt-5.3-codex | Optimized for coding tasks |
| Codex reasoning effort | xhigh | `config.model_reasoning_effort` |
| Codex sandbox | read-only | Only allow file reads |
| GLM max_iterations | 100 | GLM internal tool call limit |
| Max parallel searches | 15 | Maximum parallel searches (5-10 recommended) |
| Verification agent | oh-my-claudecode:explore | Fast read-only verification |
| Verification model | haiku | Speed-optimized for spot-checks |
| Report location | .kkachi/{branch}/completed/ | GLM report storage |
| Trial rotation | .kkachi/{branch}-trial-{n}/ | Previous attempts preserved |
| Output | TASK.md | Final deliverable |

## MCP Tools Used

| Tool | Purpose |
|------|---------|
| `submit_task` | Submit GLM research tasks |
| `wait_for_completion` | Block until tasks complete with streaming progress |
| `get_queue_stats` | Check queue status (for debugging) |
| `get_task_status` | Check individual task status (for debugging) |
| `get_task_result` | Retrieve completed task result |
| `codex` | Start GPT-5.3-Codex xhigh session |
| `codex-reply` | Continue GPT-5.3-Codex conversation |

## Example Usage

```
User: "Design TASK-177"

Claude:
1. Setup workspace (rotate .kkachi/task-177 → .kkachi/task-177-trial-1)
2. codex() call → generates 8 search queries
3. submit_task x 8 → GLM parallel search
4. wait_for_completion(task_ids) → streams progress:
   🚀 [abc123] Started
   🚀 [def456] Started
   ⚠️ [abc123] Rate limited (retry 1/10), waiting 1 min...
   ✅ [def456] Completed
   ...
   🏁 Done: 8 completed
5. Spawn 8 verification subagents (parallel, haiku)
   → Each reviews one report
   → Appends Reviewer's Note to report

[Verification complete - example notes:]
- 6 reports: "All claims verified ✓"
- 2 reports: "Issues Found ⚠️" with corrections

Claude:
6. codex-reply() → synthesize reports
   → Extra attention to 2 flagged reports
   → Cross-verify issues before including
   → Create draft
7. User interview 3 rounds
8. codex-reply() → generate TASK.md draft
9. "Please review the TASK.md..."

User: "Why did you choose X over Y?"
GPT-5.3-Codex: [explains reasoning]

User: "Add more detail to the testing section."
GPT-5.3-Codex: [updates TASK.md, explains changes]

User: "승인"
GPT-5.3-Codex: "TASK.md finalized. Workflow complete."

Output: TASK.md (user-approved, execution-ready)
```

## Trial History

When running multiple iterations on the same branch:

```
.kkachi/
├── task-177/              # Current (fresh) workspace
│   └── completed/
│       ├── 01-proto-analysis.md
│       └── 02-service-layer.md
├── task-177-trial-1/      # First attempt
│   └── completed/
│       └── ...
├── task-177-trial-2/      # Second attempt
│   └── completed/
│       └── ...
└── other-branch/
    └── completed/
```

This preserves history for reference and debugging.

## Notes

- Division of labor: GPT-5.3-Codex xhigh designs, Opus implements
- **TASK.md quality directly determines implementation quality**
- Write TASK.md as if the reader has ZERO context about the project
- Vague TASK.md → Opus guesses → bugs and rework
- Never skip interviews - ambiguous decisions cause rework later
- Blocking + streaming provides real-time progress without polling
- Progress auto-reported: task started, completed, rate-limited, retrying
- Verification catches GLM hallucinations before they pollute the plan
- Haiku is fast enough for spot-check verification (read-only operations)
- Reports with "Issues Found" get extra scrutiny during synthesis
- **User approval is mandatory** - workflow only completes after explicit approval
- Approval loop allows user to ask questions and request changes before finalizing
- **Use AskUserQuestion for all choice questions** - better UX with keyboard navigation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seventeenthearth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
