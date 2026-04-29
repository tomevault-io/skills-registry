---
name: comment
description: Add a comment to a ClickUp task Use when this capability is needed.
metadata:
  author: thebushidocollective
---

# comment

## Name

clickup:comment - Add a comment to a ClickUp task

## Synopsis

```
/comment [arguments]
```

## Description

Add a comment to a ClickUp task

## Implementation

Add a comment to a ClickUp task.

**Usage**: `/comment #ABC123 [optional: comment text]` or `/comment ABC123 [optional: comment text]`

If comment text is not provided, prompt for it with support for multi-line input.

Use `clickup_add_comment` to add the comment.

**Display Format**:

```
💬 Adding comment to #ABC123

Comment:
{comment text}

Posted successfully!

View task: {task URL}
```

Support markdown formatting in comments:

- Bold: **text**
- Italic: *text*
- Code: `code`
- Code blocks: ```language
- Lists
- Links
- Task mentions: #taskid

Provide formatting tips if user is writing a longer comment.

Optionally ask if they want to notify all task followers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebushidocollective) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
