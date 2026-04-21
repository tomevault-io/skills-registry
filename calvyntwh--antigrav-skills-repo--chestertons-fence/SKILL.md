---
name: chestertons-fence
description: Use when working with a safety protocol for refactoring. Do not remove a fence until you know why it was put up. Use when deleting, simplifying, or "fixing" code you do not fully understand.
metadata:
  author: calvyntwh
---

# Chesterton's Fence (The Context Audit)

> "In the matter of reforming things, as distinct from deforming them, there is one plain and simple principle... The more modern type of reformer goes gaily up to it and says, 'I don't see the use of this; let us clear it away.' To which the more intelligent type of reformer will do well to answer: 'If you don't see the use of it, I certainly won't let you clear it away. Go away and think. Then, when you can come back and tell me that you do see the use of it, I may allow you to destroy it.'" - G.K. Chesterton

## When to Use
*   **Refactoring:** When removing "ugly" code, "weird" checks, or "dead" logic.
*   **Simplifying:** When merging classes or functions.
*   **Debugging:** When "optimizing" a function breaks a seemingly unrelated feature.

## The Protocol: The Context Audit
**Constraint:** You are FORBIDDEN from deleting code if you cannot explain why it was added.

### 1. The Pause (Inversion)
Stop. Assume the previous developer was smart.
*   *Theory:* This code exists to prevent a specific, nasty bug.
*   *Action:* Do not delete. Investigate.

### 2. The Archaeology (Territory)
Do not guess. Find the evidence.
*   **Git Blame:** `git blame <file> -L <line_start>,<line_end>`
    *   *Goal:* Find the Commit Hash and Author.
*   **Git Show:** `git show <commit_hash>`
    *   *Goal:* Read the Commit Message. Look for "Fixes #123" or "Hotfix for edge case".
*   **Search:** grep the codebase for the Ticket Number or related keywords.

### 3. The Interrogation
Ask the simple questions:
*   "What triggered this change?"
*   "What edge case were they fighting?"
*   "Is that edge case still possible today?"

### 4. The Decision (System Loop)
Compare the Past Context with the Present Reality.

| Discovery | Action | Protocol |
| :--- | :--- | :--- |
| **I found the reason, and it is DEFINITELY obsolete.** | **DELETE** | Document the specific reason for deletion in your commit/PR. |
| **I found the reason, and the risk is real.** | **KEEP & DOCUMENT** | The Map was wrong. Add a comment explaining *why* this "ugly" code saves the system. |
| **I cannot find the reason.** | **DO NOT DELETE** | Flag for human review. It is a "load-bearing fence". |

## Example: The "Ugly" Null Check

**Code:**
```javascript
if (user && user.id && user.id !== 0) { ... } // Ugly!
```

**Impulse (Occam's Razor):** "Let's clean this up to `if (user?.id)`."

**The Fence (Investigation):**
*   `git blame` -> Commit `a1b2c3d` by `@dave`.
*   `git show a1b2c3d` -> "Fix bug where Legacy API returns `id: 0` for admin users, causing permission bypass."

**The Result:**
*   `user?.id` would return `0` (falsy), blocking admins.
*   **Action:** KEEP the check. Update the comment: `// strict check needed for Legacy API id:0 admins`.

## Resources
*   [Detailed Research Notes](references/research.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calvyntwh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
