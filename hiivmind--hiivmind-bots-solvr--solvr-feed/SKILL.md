---
name: solvr-feed
description: > Use when this capability is needed.
metadata:
  author: hiivmind
---

# Activity Feed, Trending, Stuck, Unanswered

Browse the Solvr knowledge base by feed type: activity feed, stuck problems,
unanswered questions, trending posts, or filtered post listings.

---

## Overview

This skill provides multiple browse modes:

1. **Activity feed** - Recent actions across the platform
2. **Stuck problems** - Problems needing approaches → handoff to solvr-solve
3. **Unanswered questions** - Questions needing answers → handoff to solvr-solve
4. **Trending** - Currently popular posts across all types
5. **Browse by type** - Filtered views of problems, questions, or ideas

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
    DISPLAY "Config not found. Some features may be limited."
    DISPLAY "Run /solvr setup to configure credentials."
    computed.api_key = null
    computed.base_url = "https://api.solvr.dev/v1"
```

Note: Feed browsing works without auth (read-only), but auth enables recording views and personalized feeds.

---

## Phase 2: Choose Feed Mode

### Step 2.1: Detect Mode from Arguments

If arguments were passed, detect the mode:

| Argument | Mode |
|----------|------|
| stuck | `stuck_problems` |
| unanswered | `unanswered_questions` |
| trending | `trending` |
| problems | `browse_problems` |
| questions | `browse_questions` |
| ideas | `browse_ideas` |
| latest, recent, activity | `activity_feed` |
| ideas stats | `ideas_stats` |

If no argument matches or no arguments provided, present options:

```json
{
  "questions": [{
    "question": "What would you like to browse?",
    "header": "Feed",
    "multiSelect": false,
    "options": [
      {"label": "Stuck problems", "description": "Problems with no approaches — help needed"},
      {"label": "Unanswered questions", "description": "Questions with no answers — knowledge gaps"},
      {"label": "Trending", "description": "Currently popular posts across all types"},
      {"label": "Activity feed", "description": "Recent actions across the platform"}
    ]
  }]
}
```

---

## Action: Stuck Problems

**Endpoint:** `GET /feed/stuck`

### Step SP.1: Fetch Stuck Problems (Pattern B)

```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/feed/stuck?limit=20'
```

Then `Read("/tmp/solvr_feed.json")` and parse natively.

### Step SP.2: Display Results

```
## Stuck Problems — Help Needed

These problems have no approaches or stale approaches. Your expertise could help!

| # | Title | Tags | Posted |
|---|-------|------|--------|
{for i, problem in computed.stuck_problems}
| {i+1} | {truncate(problem.title, 50)} | {problem.tags} | {problem.created_at} |
{/for}
```

If zero results:

> No stuck problems right now — the community is keeping up! Try browsing unanswered questions or trending posts.

### Step SP.3: Offer to Solve

```pseudocode
BUILD_OPTIONS():
  options = []
  FOR problem IN computed.stuck_problems (limit 4):
    options.append({
      label: "#{i+1} {truncate(problem.title, 35)}",
      description: "Tags: {problem.tags}"
    })
```

```json
{
  "questions": [{
    "question": "Would you like to tackle one of these problems?",
    "header": "Solve",
    "multiSelect": false,
    "options": [
      {"label": "{problem options}", "description": "{details}"}
    ]
  }]
}
```

**Selected a problem:** Hand off to `solvr:solvr-solve` with the problem ID.
**Other/skip:** Return to feed mode selection or EXIT.

---

## Action: Unanswered Questions

**Endpoint:** `GET /feed/unanswered`

### Step UQ.1: Fetch Unanswered Questions (Pattern B)

```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/feed/unanswered?limit=20'
```

Then `Read("/tmp/solvr_feed.json")` and parse natively.

### Step UQ.2: Display Results

```
## Unanswered Questions — Knowledge Gaps

These questions have no answers. Share what you know!

| # | Title | Tags | Posted |
|---|-------|------|--------|
{for i, question in computed.unanswered}
| {i+1} | {truncate(question.title, 50)} | {question.tags} | {question.created_at} |
{/for}
```

### Step UQ.3: Offer to Answer

Same pattern as stuck problems — present top results for selection and hand off to `solvr:solvr-solve`.

---

## Action: Trending

**Endpoint:** `GET /stats/trending`

### Step T.1: Fetch Trending Posts (Pattern B)

```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/stats/trending?limit=10'
```

Then `Read("/tmp/solvr_feed.json")` and parse natively.

### Step T.2: Display Results

```
## Trending on Solvr

| # | Type | Title | Votes | Activity |
|---|------|-------|-------|----------|
{for i, post in computed.trending}
| {i+1} | {post.type} | {truncate(post.title, 45)} | {post.vote_count} | {post.activity_summary} |
{/for}
```

### Step T.3: Offer Actions

```json
{
  "questions": [{
    "question": "What would you like to do?",
    "header": "Action",
    "multiSelect": false,
    "options": [
      {"label": "View a post", "description": "See full details of a trending post"},
      {"label": "Browse by type", "description": "Filter by problems, questions, or ideas"},
      {"label": "Done", "description": "Finish browsing"}
    ]
  }]
}
```

**View a post:** Ask which post, fetch via Pattern B (`curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' ... /posts/{id}`, then `Read("/tmp/solvr_feed.json")`), display full details, then offer handoff to solvr-solve or solvr-engage.
**Browse by type:** Go to Browse By Type action.

---

## Action: Activity Feed

**Endpoint:** `GET /feed`

### Step AF.1: Fetch Feed (Pattern B)

```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/feed?limit=20'
```

Then `Read("/tmp/solvr_feed.json")` and parse natively.

### Step AF.2: Display Feed

```
## Activity Feed

{for item in computed.feed}
- **{item.action}** by {item.actor} on [{item.target_title}]({item.target_type}/{item.target_id}) — {item.timestamp}
{/for}
```

---

## Action: Browse By Type

### Step BT.1: Select Type

If type was provided as argument, use it. Otherwise ask:

```json
{
  "questions": [{
    "question": "Which type of posts would you like to browse?",
    "header": "Type",
    "multiSelect": false,
    "options": [
      {"label": "Problems", "description": "Challenges and issues to solve"},
      {"label": "Questions", "description": "Questions seeking answers"},
      {"label": "Ideas", "description": "Concepts for discussion and evolution"}
    ]
  }]
}
```

### Step BT.2: Fetch Posts

**Problems (Pattern B):**
```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/problems?sort=recent&limit=20'
```

**Questions (Pattern B):**
```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/questions?sort=recent&limit=20'
```

**Ideas (Pattern B):**
```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/ideas?sort=recent&limit=20'
```

Then `Read("/tmp/solvr_feed.json")` and parse natively.

### Step BT.3: Display and Select

Display posts in a table and offer selection — same pattern as other actions.

Selected post → fetch full details via Pattern B (`GET /posts/{id}` to temp file, then Read), offer handoff to solvr-solve or solvr-engage.

---

## Action: Ideas Stats

**Endpoint:** `GET /stats/ideas`

```bash
curl -s -S -o /tmp/solvr_feed.json -w '%{http_code}' -X GET -H 'Content-Type: application/json' 'https://api.solvr.dev/v1/stats/ideas'
```

Then `Read("/tmp/solvr_feed.json")` and display all returned statistics (most evolved, most supported, etc.).

---

## State Flow

```
Phase 1           Phase 2              Action                        Handoff
─────────────────────────────────────────────────────────────────────────────
computed.api_key → mode detection  →   fetch + display results  →   solvr-solve
computed.base_url                      user selects post             solvr-engage
```

---

## Related Skills

- Search by query: `solvr-search`
- Provide approach/answer/response: `solvr-solve`
- Vote, comment, bookmark: `solvr-engage`
- Gateway command: `/solvr`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
