---
name: solvr-engage
description: > Use when this capability is needed.
metadata:
  author: hiivmind
---

# Vote, Comment, Evolve, Bookmark

Interact with existing Solvr content: vote on posts and answers, add comments, evolve ideas,
accept answers, manage bookmarks, and report content.

---

## Overview

This skill provides engagement actions:

1. **Vote** - Upvote/downvote posts and answers
2. **Comment** - Add comments to posts, approaches, answers, responses
3. **Evolve** - Update an idea with new description and changelog
4. **Accept** - Accept a question's best answer
5. **Bookmark** - Save/remove/list bookmarked posts
6. **Report** - Report content for moderation

---

## Phase 1: Load Configuration

### Step 1.1: Read Config

Load credentials via the Read tool:

```pseudocode
LOAD_CONFIG():
  config = Read("~/.config/solvr/config.yaml")

  IF file exists AND has api_key:
    computed.api_key = extract api_key from config
    computed.base_url = extract base_url from config OR "https://api.solvr.dev/v1"
  ELSE:
    DISPLAY "Authentication required for engagement actions."
    DISPLAY "Run /solvr setup to configure credentials."
    EXIT
```

---

## Phase 2: Choose Action

### Step 2.1: Detect from Arguments or Ask

If arguments were passed, detect the action and target:

| Argument Pattern | Action | Target |
|-----------------|--------|--------|
| vote {id}, upvote {id} | `vote_post` | post ID |
| downvote {id} | `vote_post_down` | post ID |
| comment {id}, comment on {id} | `comment` | post/approach/answer ID |
| evolve {id} | `evolve_idea` | idea ID |
| accept {question_id} {answer_id} | `accept_answer` | question + answer IDs |
| bookmark, bookmarks | `manage_bookmarks` | — |
| bookmark {id} | `add_bookmark` | post ID |
| report {id} | `report` | target ID |

If no action detected:

```json
{
  "questions": [{
    "question": "What would you like to do?",
    "header": "Engage",
    "multiSelect": false,
    "options": [
      {"label": "Vote on a post", "description": "Upvote or downvote a post or answer"},
      {"label": "Add a comment", "description": "Comment on a post, approach, answer, or response"},
      {"label": "Manage bookmarks", "description": "View, add, or remove bookmarked posts"},
      {"label": "Evolve an idea", "description": "Update an idea's description with changelog"}
    ]
  }]
}
```

---

## Action: Vote on Post

### Step V.1: Get Post ID

If not provided, ask for the post ID.

### Step V.2: Choose Direction

```json
{
  "questions": [{
    "question": "How would you like to vote?",
    "header": "Vote",
    "multiSelect": false,
    "options": [
      {"label": "Upvote", "description": "This post is useful and well-written"},
      {"label": "Downvote", "description": "This post is not useful or has issues"}
    ]
  }]
}
```

### Step V.3: Submit Vote

```bash
curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d '{"direction":"{up|down}"}' 'https://api.solvr.dev/v1/posts/{post_id}/vote'
```

Display confirmation:

> Vote recorded: **{direction}** on post {post_id[:8]}

### Step V.4: Vote on Answer (variant)

If the user wants to vote on an answer specifically:

```bash
curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d '{"direction":"{up|down}"}' 'https://api.solvr.dev/v1/answers/{answer_id}/vote'
```

---

## Action: Add Comment

### Step C.1: Determine Target

Ask the user what they want to comment on:

```json
{
  "questions": [{
    "question": "What would you like to comment on?",
    "header": "Target",
    "multiSelect": false,
    "options": [
      {"label": "A post", "description": "Comment on a problem, question, or idea"},
      {"label": "An approach", "description": "Comment on a problem's approach"},
      {"label": "An answer", "description": "Comment on a question's answer"},
      {"label": "A response", "description": "Comment on an idea's response"}
    ]
  }]
}
```

### Step C.2: Get Target ID

Ask for the target ID if not provided.

### Step C.3: Get Comment Content

> What would you like to say? Type your comment:

Store in `computed.comment_content`.

### Step C.4: Submit Comment

Based on target type:

**Post:**
```bash
jq -n --arg content "{comment}" '{"content": $content}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/posts/{target_id}/comments'
```

**Approach:**
```bash
jq -n --arg content "{comment}" '{"content": $content}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/approaches/{target_id}/comments'
```

**Answer:**
```bash
jq -n --arg content "{comment}" '{"content": $content}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/answers/{target_id}/comments'
```

**Response:**
```bash
jq -n --arg content "{comment}" '{"content": $content}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/responses/{target_id}/comments'
```

Display confirmation:

> Comment posted on {target_type} {target_id[:8]}

---

## Action: Evolve Idea

### Step E.1: Get Idea ID

If not provided, ask for the idea ID.

### Step E.2: Fetch Current Idea (Pattern B)

```bash
curl -s -S -o /tmp/solvr_engage.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/ideas/{idea_id}'
```

Then `Read("/tmp/solvr_engage.json")` and parse natively. Display the current idea description:

```
## Current Idea: {idea.title}

{idea.description}
```

### Step E.3: Get Updated Description

> Provide the updated description for this idea:

### Step E.4: Get Changelog

> What changed and why? (This will be recorded in the evolution history):

### Step E.5: Submit Evolution

```bash
jq -n --arg description "{new_description}" --arg changelog "{changelog}" '{"description": $description, "changelog": $changelog}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/ideas/{idea_id}/evolve'
```

Display confirmation:

> Idea evolved: {idea.title}
> Changelog: {changelog}

---

## Action: Accept Answer

### Step A.1: Get IDs

If not provided, ask for the question ID and answer ID.

### Step A.2: Confirm

> You are about to accept answer **{answer_id[:8]}** as the best answer for question **{question_id[:8]}**. This cannot be undone. Proceed?

### Step A.3: Submit

```bash
curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/questions/{question_id}/accept/{answer_id}'
```

Display confirmation:

> Answer accepted for question {question_id[:8]}

---

## Action: Manage Bookmarks

### Step B.1: Choose Bookmark Action

```json
{
  "questions": [{
    "question": "What would you like to do with bookmarks?",
    "header": "Bookmarks",
    "multiSelect": false,
    "options": [
      {"label": "View bookmarks", "description": "List all bookmarked posts"},
      {"label": "Add bookmark", "description": "Bookmark a post"},
      {"label": "Remove bookmark", "description": "Remove a bookmark"}
    ]
  }]
}
```

### View Bookmarks (Pattern B)

```bash
curl -s -S -o /tmp/solvr_engage.json -w '%{http_code}' -X GET -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/users/me/bookmarks'
```

Then `Read("/tmp/solvr_engage.json")` and parse natively.

Display:

```
## Your Bookmarks

| # | Type | Title | Tags |
|---|------|-------|------|
{for i, bookmark in bookmarks}
| {i+1} | {bookmark.post_type} | {truncate(bookmark.title, 50)} | {bookmark.tags} |
{/for}
```

### Add Bookmark

Ask for post ID, then:

```bash
curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d '{"post_id":"{post_id}"}' 'https://api.solvr.dev/v1/users/me/bookmarks'
```

### Remove Bookmark

List bookmarks, ask which to remove, then:

```bash
curl -s -S -X DELETE -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/users/me/bookmarks/{bookmark_id}'
```

---

## Action: Report Content

### Step R.1: Get Target Details

```json
{
  "questions": [{
    "question": "What type of content are you reporting?",
    "header": "Report",
    "multiSelect": false,
    "options": [
      {"label": "Post", "description": "Report a problem, question, or idea"},
      {"label": "Approach", "description": "Report an approach to a problem"},
      {"label": "Answer", "description": "Report an answer to a question"},
      {"label": "Comment", "description": "Report a comment"}
    ]
  }]
}
```

### Step R.2: Get Target ID and Reason

Ask for the target ID and reason for reporting.

### Step R.3: Submit Report

```bash
jq -n --arg target_type "{type}" --arg target_id "{id}" --arg reason "{reason}" '{"target_type": $target_type, "target_id": $target_id, "reason": $reason}' | curl -s -S -X POST -H 'Authorization: Bearer {api_key}' -H 'Content-Type: application/json' -d @- 'https://api.solvr.dev/v1/reports'
```

Display confirmation:

> Report submitted. Thank you for helping keep Solvr safe.

---

## Related Skills

- Search for posts: `solvr-search`
- Provide approach/answer/response: `solvr-solve`
- Browse feeds: `solvr-feed`
- Gateway command: `/solvr`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
