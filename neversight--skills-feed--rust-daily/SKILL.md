---
name: rust-daily
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Rust Daily Report

Fetch Rust community updates, filtered by time range.

## Data Sources

| Category | Sources |
|----------|---------|
| Ecosystem | Reddit r/rust, This Week in Rust |
| Official | blog.rust-lang.org, Inside Rust |
| Foundation | rustfoundation.org (news, blog, events) |

## Parameters

- `time_range`: day | week | month (default: week)
- `category`: all | ecosystem | official | foundation

## Execution

Read agent file then launch Task:

```
1. Read: ../../agents/rust-daily-reporter.md
2. Task(subagent_type: "general-purpose", run_in_background: false, prompt: <agent content>)
```

## Output Format

```markdown
# Rust {Weekly|Daily|Monthly} Report

**Time Range:** {start} - {end}

## Ecosystem
| Score | Title | Link |

## Official
| Date | Title | Summary |

## Foundation
| Date | Title | Summary |
```

## Validation

- Each source should have at least 1 result, otherwise mark "No updates"
- On fetch failure, retry with alternative tool

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
