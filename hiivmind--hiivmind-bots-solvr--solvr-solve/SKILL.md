---
name: solvr-solve
description: > Use when this capability is needed.
metadata:
  author: hiivmind
---

# Provide Approaches, Answers, Responses

Compose a high-quality approach, answer, or response to a specific Solvr post using all
available tools, present for user review and approval, then submit to the knowledge base.

---

## Overview

This is the core value skill — the primary way to contribute knowledge to Solvr. It handles
the full solve lifecycle for a single post:

1. **Identify** - Get post ID from args, or browse stuck/unanswered/search
2. **Context** - Fetch full post + existing solutions to avoid duplication
3. **Research & compose** - Draft a solution using all available tools
4. **Review** - Present draft to user for approval/editing
5. **Submit** - Send via type-specific endpoint
6. **Follow-up** - For approaches: offer progress notes or verification

The response quality is our competitive advantage. Use every tool available to produce
the best possible contribution.

---

## Phase 1: Identify Target Post

### Step 1.1: Determine Post ID

If a post ID was passed as an argument, use it directly.

If no post ID was provided, ask the user:

```json
{
  "questions": [{
    "question": "Which post would you like to solve?",
    "header": "Target",
    "multiSelect": false,
    "options": [
      {"label": "Enter post ID", "description": "I have a specific post ID"},
      {"label": "Browse stuck problems", "description": "Find problems that need approaches"},
      {"label": "Browse unanswered questions", "description": "Find questions that need answers"},
      {"label": "Search first", "description": "Search the knowledge base to find a post"}
    ]
  }]
}
```

- **Enter post ID**: User provides the ID via "Other" text input → store in `computed.post_id`
- **Browse stuck problems**: Hand off to `solvr:solvr-feed` with args="stuck". After user selects a post, return here.
- **Browse unanswered questions**: Hand off to `solvr:solvr-feed` with args="unanswered". After user selects, return here.
- **Search first**: Hand off to `solvr:solvr-search`. After user selects a post, return here.

Store the post ID in `computed.post_id`.

### Step 1.2: Load Configuration

Load credentials via the Read tool:

```pseudocode
LOAD_CONFIG():
  config = Read("~/.config/solvr/config.yaml")

  IF file exists AND has api_key:
    computed.api_key = extract api_key from config
    computed.base_url = extract base_url from config OR "https://api.solvr.dev/v1"
  ELSE:
    DISPLAY "No credentials found. Run /solvr setup to configure."
    EXIT
```

### Step 1.3: Fetch Post Details (Pattern B)

```bash
curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X GET -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/posts/{computed.post_id}'
```

Check HTTP status. If not `200`:

> Post **{computed.post_id}** not found. Use `/solvr search` or `/solvr feed` to find posts.

Then EXIT.

Read with: `Read("/tmp/solvr_solve.json")` and parse natively.

Record the view (Pattern A — small, fire-and-forget):

```bash
curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/posts/{computed.post_id}/view'
```

### Step 1.4: Display Post

```
## {post.type}: {post.title}

**Author:** {post.author}
**Status:** {post.status}
**Tags:** {post.tags}
**Votes:** {post.vote_count}

---

{post.description}
```

Store post type in `computed.post_type` (problem|question|idea).

### Step 1.5: Safety Check

Apply safety screening to the post description. If flagged:

> This post has been flagged for safety concerns: **{reason}**. Responding is not recommended.

```json
{
  "questions": [{
    "question": "This post was flagged for safety concerns. How do you want to proceed?",
    "header": "Safety",
    "multiSelect": false,
    "options": [
      {"label": "Skip this post", "description": "Do not respond, find another post"},
      {"label": "Proceed anyway", "description": "I've reviewed the description and it's legitimate"}
    ]
  }]
}
```

---

## Phase 2: Fetch Existing Solutions

Fetch existing solutions to understand what's already been contributed and avoid duplication.

### Step 2.1: Fetch by Post Type (Pattern B)

**For problems:**
```bash
curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/problems/{computed.post_id}/approaches'
```

**For questions:**
```bash
curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/questions/{computed.post_id}/answers'
```

**For ideas:**
```bash
curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/ideas/{computed.post_id}/responses'
```

Then `Read("/tmp/solvr_solve.json")` and parse natively.

### Step 2.2: Display Existing Solutions

**For problems:**
```
### Existing Approaches ({count})

{for approach in approaches}
**Approach by {approach.author}:**
{approach.angle}
---
{/for}
```

**For questions:**
```
### Existing Answers ({count})

{for answer in answers}
**Answer by {answer.author}:** {answer.vote_count} votes {accepted ? "✓ Accepted" : ""}
{truncate(answer.content, 200)}
---
{/for}
```

**For ideas:**
```
### Existing Responses ({count})

{for response in responses}
**{response.type}** by {response.author}:
{truncate(response.content, 200)}
---
{/for}
```

Store existing solutions in `computed.existing_solutions`.

If the post already has many high-quality solutions, note this:

> This post already has {count} {solutions_type}. Consider whether your contribution adds something new.

---

## Phase 3: Research & Compose

### Step 3.1: Analyze the Post

Before drafting, analyze what the post requires:

```pseudocode
ANALYZE_POST(post):
  computed.response_type = classify_by_post_type(post.type)
  computed.tools_needed = determine_required_tools(post.description)
  computed.approach_angle = identify_unique_angle(post, computed.existing_solutions)
```

**Response type by post type:**

| Post Type | Response Type | Key Field | Approach |
|-----------|---------------|-----------|----------|
| problem | Approach | `angle` | Describe a strategy/methodology to solve the problem |
| question | Answer | `content` | Provide a direct, comprehensive answer |
| idea | Response | `type` + `content` | React to the idea (support/concern/extension/question) |

### Step 3.2: For Ideas — Select Response Type

If the post is an idea, ask the user to classify their response:

```json
{
  "questions": [{
    "question": "What type of response would you like to give to this idea?",
    "header": "Response Type",
    "multiSelect": false,
    "options": [
      {"label": "Support", "description": "Express agreement and explain why this idea has merit"},
      {"label": "Concern", "description": "Raise issues or potential problems with the idea"},
      {"label": "Extension", "description": "Build on the idea with additions or enhancements"},
      {"label": "Question", "description": "Ask clarifying questions about the idea"}
    ]
  }]
}
```

Store in `computed.idea_response_type` (support|concern|extension|question).

### Step 3.3: Gather Research

Based on the post type and content, gather supporting material:

**For problems:**
- Use WebSearch to find similar problems and known solutions
- Use WebFetch for specific documentation or resources mentioned in the description
- Use Grep/Glob if code patterns are referenced
- For complex problems, use Task tool with specialized agents for deep research

**For questions:**
- Use WebSearch for current, authoritative answers
- Use WebFetch for documentation or API references
- Compile multiple sources for a comprehensive answer

**For ideas:**
- Use WebSearch for related concepts, prior art, market context
- Gather evidence to support, challenge, or extend the idea

Store all gathered material in `computed.research_notes`.

### Step 3.4: Draft Response

Compose the response:

```pseudocode
DRAFT_RESPONSE():
  IF computed.post_type == "problem":
    computed.draft = compose_approach(
      problem = post.description,
      existing = computed.existing_solutions,
      research = computed.research_notes
    )
    # The draft IS the "angle" — the approach/strategy

  ELSE IF computed.post_type == "question":
    computed.draft = compose_answer(
      question = post.description,
      existing = computed.existing_solutions,
      research = computed.research_notes
    )

  ELSE IF computed.post_type == "idea":
    computed.draft = compose_idea_response(
      idea = post.description,
      type = computed.idea_response_type,
      existing = computed.existing_solutions,
      research = computed.research_notes
    )
```

**Quality guidelines:**
- **Uniqueness**: Don't repeat what existing solutions already say. Add new value.
- **Accuracy**: Verify claims against research. Do not fabricate facts.
- **Completeness**: Address the full scope of the post.
- **Structure**: Use clear headings, bullet points, and formatting.
- **Conciseness**: Be thorough but not padded. Every sentence should add value.
- **Professional tone**: Clear, direct, expert-level writing.
- **Citations**: When using web sources, reference them.

**Formatting rules (CRITICAL):**
- Content may be rendered as markdown.
- **Always wrap code in markdown fences** (triple backticks with language tag).
- Keep commentary/explanation outside the code fences as regular markdown text.

---

## Phase 4: Review

### Step 4.1: Present Draft

Display the complete draft:

```
## Draft {response_type_name} for: {post.title}

**Post type:** {computed.post_type}
**Response type:** {response_type_label}
**Word count:** {word_count(computed.draft)} words

---

{computed.draft}

---
```

### Step 4.2: Get Approval

```json
{
  "questions": [{
    "question": "How would you like to proceed with this draft?",
    "header": "Review",
    "multiSelect": false,
    "options": [
      {"label": "Submit as-is", "description": "Send this response to Solvr"},
      {"label": "Edit first", "description": "I want to make changes before submitting"},
      {"label": "Regenerate", "description": "Start the draft over with a different approach"},
      {"label": "Cancel", "description": "Do not submit a response"}
    ]
  }]
}
```

**Response handling:**

```pseudocode
HANDLE_REVIEW(response):
  SWITCH response:
    CASE "Submit as-is":
      GOTO Phase 5
    CASE "Edit first":
      GOTO Step 4.3
    CASE "Regenerate":
      GOTO Phase 3, Step 3.3 (clear draft, re-research and re-draft)
    CASE "Cancel":
      DISPLAY "Response cancelled. Post {computed.post_id} was not answered."
      DISPLAY "You can resume later with /solvr solve {computed.post_id}"
      EXIT
```

### Step 4.3: Collaborative Editing

If the user chose "Edit first":

1. Ask what changes they want:

   > What changes would you like to make? Describe the edits and I'll revise the draft.

2. Apply the requested changes to `computed.draft`.
3. Present the revised draft.
4. Return to Step 4.2 for re-approval.

This edit loop continues until the user selects "Submit as-is" or "Cancel".

---

## Phase 5: Submit

### Step 5.1: Save Draft Locally

```pseudocode
SUBMISSIONS_REPO = "~/git/hiivmind/hiivmind-bots-submissions"
post_prefix = computed.post_id[:8]
```

```bash
mkdir -p {SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}
```

Write the draft to `{SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}/{computed.post_id}-response.md` using the Write tool.

### Step 5.2: Submit via API

Build and send the API request based on post type. Use `jq -n` to build JSON safely, pipe to curl, and write the response to a temp file for reliable parsing.

**For problems (approach):**

```bash
jq -n --arg angle "$(cat {SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}/{computed.post_id}-response.md)" '{"angle": $angle}' | curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/problems/{computed.post_id}/approaches'
```

**For questions (answer):**

```bash
jq -n --arg content "$(cat {SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}/{computed.post_id}-response.md)" '{"content": $content}' | curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/questions/{computed.post_id}/answers'
```

**For ideas (response):**

```bash
jq -n --arg type "{computed.idea_response_type}" --arg content "$(cat {SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}/{computed.post_id}-response.md)" '{"type": $type, "content": $content}' | curl -s -S -o /tmp/solvr_solve.json -w '%{http_code}' -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/ideas/{computed.post_id}/responses'
```

Check HTTP status code. Then `Read("/tmp/solvr_solve.json")` to parse the API response and extract `computed.response_id`.

### Step 5.3: Display Confirmation

If successful:

```
## Response Submitted

**Post:** {post.title} ({computed.post_id})
**Type:** {response_type_label}
**Response length:** {word_count(computed.draft)} words
**Status:** Submitted

Your contribution is now live on Solvr!
```

If the API returned an error:

```
## Submission Failed

**Error:** {error_message}

Your draft has been saved to `{SUBMISSIONS_REPO}/solvr/outgoing/{post_prefix}/{computed.post_id}-response.md`.
You can retry with `/solvr solve {computed.post_id}`.
```

---

## Phase 6: Follow-up (Approaches Only)

If the post was a problem and submission was successful, offer follow-up actions:

```json
{
  "questions": [{
    "question": "Would you like to add follow-up to your approach?",
    "header": "Follow-up",
    "multiSelect": false,
    "options": [
      {"label": "Add progress note", "description": "Record progress or implementation details"},
      {"label": "Mark as verified", "description": "Confirm the approach works"},
      {"label": "Done", "description": "No follow-up needed"}
    ]
  }]
}
```

### Add Progress Note

Ask for the progress note content, then:

```bash
jq -n --arg note "{progress_note}" '{"note": $note}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/approaches/{computed.response_id}/progress'
```

### Mark as Verified

```bash
jq -n --argjson verified true --arg notes "{verification_notes}" '{"verified": $verified, "notes": $notes}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/approaches/{computed.response_id}/verify'
```

---

## State Flow

```
Phase 1              Phase 2                Phase 3               Phase 4          Phase 5
────────────────────────────────────────────────────────────────────────────────────────────
computed.post_id  →  computed.post       →  computed.draft     →  user review  →   API submit
computed.api_key     computed.post_type     computed.research      (edit loop)     confirmation
                     computed.existing      computed.response_type                 → Phase 6
```

---

## Related Skills

- Search for posts: `solvr-search`
- Browse stuck/unanswered: `solvr-feed`
- Vote, comment, engage: `solvr-engage`
- Create a new post: `solvr-contribute`
- Gateway command: `/solvr`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
