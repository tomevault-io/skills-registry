---
name: reminders
description: Set a reminder for the current user on an issue, document, project, or initiative. Use when this capability is needed.
metadata:
  author: rayhanadev
---

<time_handling>

- Absolute date -> triggers at 9am in user's timezone.
- Absolute datetime -> triggers at that exact time.
- Duration ("in 2 hours") -> triggers relative to now.
- Next weekday/week -> triggers next occurrence at 9am.
  </time_handling>

<behavior>
- Only for the current user (can't set for teammates).
- One reminder per entity; setting a new one replaces the old.
- Search for the target entity first via search_entities.
</behavior>

<alternative>
- For deadline-based reminders, use update_issue with dueDate instead.
</alternative>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rayhanadev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
