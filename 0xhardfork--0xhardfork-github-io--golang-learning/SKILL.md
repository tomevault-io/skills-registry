---
name: golang-learning
description: Daily Golang learning skill that generates a focused topic with concept explanation, code examples, and practice exercises. Use when user says "开始今日任务", "start today's task", "start daily task", or any equivalent. Tracks learning history in history.yaml to avoid repeating topics. Use when this capability is needed.
metadata:
  author: 0xHardfork
---

# Golang Daily Learning Skill

This skill helps you learn Go (Golang) one topic per day. Each session covers a single concept with a clear explanation, runnable code examples, and hands-on exercises.

## When to Use This Skill

- User says **"开始今日golang任务"** (start today's golang task)
- User says **"start today's golang task"** or **"start daily golang task"**
- User says **"begin daily golang practice"** or **"golang today"**
- User wants to continue their daily Go learning routine

## Workflow

### Step 1 — Read History

Before generating any content:

1. Read `history.yaml` from the skill directory (`.agent/skills/golang-learning/history.yaml`)
2. Extract the list of all **previously covered topics** (the `topic_key` field of each entry)
3. Also note the most recent entry's date so you can inform the user of their streak

If `history.yaml` does not exist yet, treat the history as **empty** and create the file after the session.

### Step 2 — Select Today's Topic

Pick **one topic** from the curriculum below that:

- Has **NOT** appeared in `history.yaml`
- Is appropriate as the **next step** given what topics have already been covered (prefer sequential/progressive ordering)
- If all topics in a category are covered, move to the next category

**Topic Curriculum (ordered by progression):**

#### The 7-Day Ultra-Intensive Curriculum (Double Density)
| topic_key | Title | Description |
|-----------|-------|-------------|
| `day1_basics_control` | 0x01: Basics, Control Flow & Functions | Variables, `:=`, Types, `for` loops, `switch`, Multiple Return Values, and passing by pointer (`*`, `&`). |
| `day2_data_structs` | 0x02: Arrays, Slices, Maps & Structs | Fixed Arrays vs slice capabilities, `make`/`append`, Map iteration, defining Structs and Methods with pointer receivers. |
| `day3_interfaces_err` | 0x03: Interfaces & Error Handling | Implicit interfaces, empty `interface{}`, type assertions, `error` returns, `panic`/`recover`. |
| `day4_concurrency` | 0x04: Goroutines & Channels | Lightweight concurrency, spawning jobs, Buffered vs Unbuffered channels, `select`. |
| `day5_sync_context` | 0x05: Sync & Context Packages | `sync.WaitGroup`, `sync.Mutex` to prevent data races, building timeouts/cancellation with `context.Context`. |
| `day6_io_json_test` | 0x06: I/O, SerDe & Testing | JSON Encoding/Decoding, `io.Reader`/`io.Writer`, unit tests with `*testing.T`, Table-driven testing. |
| `day7_adv_internals` | 0x07: Advanced Internals & Reflection | Reflection (`reflect`), `unsafe`, escapes to heap, profiling (`pprof`), Go Memory Model basics. |

### Step 3 — Generate the Daily Learning Session

Generate a full, structured learning session directly inside the **`pages/development/golang/`** directory (the GitHub Pages site directory).

**Naming convention**: Use `YYYY-MM-DD_golang{NN}.md` where NN is a 2-digit sequence (usually 01 for the first topic of the day).
**File path**: `pages/development/golang/YYYY-MM-DD_golang01.md`  
(e.g., `pages/development/golang/2026-03-08_golang01.md`)

---

#### Output Format

**IMPORTANT — Jekyll Front-matter**:
Every generated `.md` file MUST begin with Jekyll front-matter so the page renders correctly on the GitHub Pages site:

```markdown
---
layout: default
title: "{Title}"
---

# Golang

## 🧠 Concept Overview

**[CRITICAL: Do NOT output any "Welcome to" or AI intros. Immediately start the first sentence with a deep technical exploration of the topic.]**

[EXTREMELY exhaustive, double-length, step-by-step explanation of the concepts. Start from the absolute basics... then dive deep. Explain the *WHY* behind Go's design in meticulous detail.]

---

## 🔑 Key Points

- [Key point 1]
- [Key point 2]
- [Key point 3]
- [Key point 4]
- [Key point 5]

---

## 💻 Code Examples

### Example 1 — {Descriptive title}

\`\`\`go
// File: example1.go
package main

import "fmt"

func main() {
    // Clear, well-commented code
    // Each step explained inline
}
\`\`\`

**What this demonstrates**: [1–2 sentence explanation of the example]

### Example 2 — {Descriptive title}

\`\`\`go
// File: example2.go
package main

import "fmt"

func main() {
    // A slightly more complex or realistic example
}
\`\`\`

**What this demonstrates**: [1–2 sentence explanation]

---

## ⚠️ Common Mistakes

| Mistake | Why it's wrong | Correct approach |
|---------|---------------|-----------------|
| [mistake 1] | [reason] | [fix] |
| [mistake 2] | [reason] | [fix] |
| [mistake 3] | [reason] | [fix] |

---

## 🏋️ Practice Exercises

### Exercise 1 to 8 (Scale appropriately)
Generate exactly 8 exercises, pacing from easy to hard. Provide detailed goals, requirements, constraints, and starter code for each.

### Exercise 1 — {Title} (Easy)
**Goal**: [Clear description of what to implement]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint without giving away the answer]

---

### Exercise 2 — {Title} (Easy)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 3 — {Title} (Medium)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 4 — {Title} (Medium)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 5 — {Title} (Medium)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 6 — {Title} (Hard)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 7 — {Title} (Hard)
**Goal**: [Clear description]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint]

---

### Exercise 8 — {Title} (Challenge!)
**Goal**: [A more realistic, real-world scenario]

**Requirements**:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]
- [Requirement 4]

**Starter code**:

\`\`\`go
package main

// TODO: Complete the implementation
\`\`\`

**Hint**: [A subtle hint pointing toward the right approach]

---

## 📖 Reference & Further Reading

- [Official Go Documentation: {relevant page}]({url})
- [Go by Example: {topic}](https://gobyexample.com/{topic})
- [Effective Go — {section}](https://go.dev/doc/effective_go#{section})

---

## 🗒️ Quick Cheat Sheet

\`\`\`go
// One-liner reference for today's topic — the most important syntax
\`\`\`

---

[← back to golang](./) | [← back to development](..) | [← back to home](/)

---

### Step 4 — Update history.yaml

After generating the session file, **append** a new entry to `history.yaml`:

```yaml
# Example structure of history.yaml
- date: "2026-03-08"
  topic_key: "day1_fundamentals_types"
  title: "0x01: Go Fundamentals & Type System"
  category: "Intensive Track"
  file: "pages/development/golang/2026-03-08_golang01.md"

- date: "2026-03-09"
  topic_key: "day2_concurrency_errors"
  title: "0x02: Concurrency & Error Philosophy"
  category: "Intensive Track"
  file: "pages/development/golang/2026-03-09_golang01.md"
```

**Rules**:
- Always append; never overwrite existing entries
- If `history.yaml` does not exist, create it with proper YAML formatting
- Use `"YYYY-MM-DD"` date string format (quoted for YAML safety)

### Step 5 — Confirm to the User

After generating the file and updating history, tell the user:

1. **Today's topic**: The title and category
2. **File created**: The path to the session file
3. **Progress**: How many topics have been covered so far (e.g., "3 / 50 topics completed")
4. **Streak reminder**: The date of their last session
5. **Quick start**: Suggest they open the file and start with the Concept Overview, then try the exercises

## Content Quality Guidelines

### Tone & Style
- **Tone**: Use a warm, human-like, and conversational tone. Act like a senior Go developer chatting with a peer. Use simple, colloquial language rather than overly formal, rigid AI-speak. Avoid robotic intros like "Welcome to today's topic" or "In this lesson..."
- **Exhaustive Content Length**: You MUST generate extremely long, exhaustive content to ensure absolutely NO knowledge points are missed. Dive deeply into every edge case, internal mechanism, and API detail. Do NOT summarize or abbreviate. Your output must be a definitive, deeply comprehensive guide.
- **Natural Flow**: Let the document flow naturally without feeling like a rigid AI textbook template.

### Concept Explanations
- **Balance Beginner & Advanced**: The user is an experienced developer but a **Complete Beginner** to Go. You MUST first construct a solid beginner-friendly foundation by thoroughly explaining Go's basic syntax (variables, loops, structs) from the ground up. However, once the basics are secure, you MUST cater to their engineering experience by diving into deep, advanced topics like internal memory layout, Go runtime behavior, and performance nuances.
- Provide step-by-step, gentle guidance. Relate Go concepts to other languages to bridge the gap (e.g., "Like in C++, but memory safe...").
- Use professional terminology but explain it conversationally.

### Code Examples
- **Volume & Variety**: Provide MULTIPLE code patterns. Do not stop at just one basic example. Show basic usage, advanced idiomatic usage, and how to handle edge cases.
- All code must be **complete and runnable** (`package main`, `func main()`, proper imports)
- Prefer complex, high-performance, and idiomatic use cases over toy examples.
- Demonstrate advanced edge cases (e.g., channel deadlocks, memory leaks via slices).

### Exercises
- **Volume**: You MUST generate exactly **8 practice exercises** per daily session. This is an absolute requirement.
- Exercises 1-3 (Easy): Basic syntax application.
- Exercises 4-6 (Medium): Implementing programming logic using today's Go features.
- Exercises 7-8 (Hard / Challenge): A complex algorithmic or data structure task combining today's topics.
- Ensure exercises build confidence step-by-step.

### Progression
- Always check `history.yaml` first — **never repeat a topic**
- Follow the 7-day curriculum order.

## File Organization

```
# Skill files (unchanged)
.agent/skills/golang-learning/
├── SKILL.md              ← This file
└── history.yaml          ← Auto-managed learning history

# Generated articles go here (GitHub Pages site)
pages/development/golang/
├── index.md                            # Directory index (auto-lists articles)
├── YYYY-MM-DD_golang01.md             # Session file for that day
└── ...
```

## Error Handling

- If `history.yaml` is not parseable, warn the user and start with an empty history
- If all 7 intensive topics are covered, congratulate the user and offer to generate custom system-design scenarios or dive into specific framework codebases.
- If the user requests a specific topic (even if already covered), generate it but note it was covered before.

---
> Source: [0xHardfork/0xHardfork.github.io](https://github.com/0xHardfork/0xHardfork.github.io) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
