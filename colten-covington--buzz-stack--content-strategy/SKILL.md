---
name: content-strategy
description: Product and UI copy guidance for tone, clarity, and consistency. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Content Strategy

Use this skill for UI copy, microcopy, error messages, empty states, and user guidance. Clear, consistent writing enhances user experience and reduces support requests.

---

## Table of Contents

1. [Voice & Tone](#voice--tone)
2. [Writing Principles](#writing-principles)
3. [Microcopy Patterns](#microcopy-patterns)
4. [Accessibility Considerations](#accessibility-considerations)
5. [Anti-Patterns to Avoid](#anti-patterns-to-avoid)

---

## Voice & Tone

### Brand Voice (Consistent)

Buzz Stack's voice is:

- **Clear** - Use simple language, avoid jargon
- **Helpful** - Guide users to success
- **Confident** - Authoritative but not arrogant
- **Concise** - Respect the user's time

### Tone (Context-Dependent)

| Context     | Tone                     | Example                                                  |
| ----------- | ------------------------ | -------------------------------------------------------- |
| Success     | Celebratory, brief       | ✅ "Profile saved!"                                      |
| Error       | Empathetic, actionable   | ⚠️ "Connection lost. Check your network and try again."  |
| Empty state | Encouraging, instructive | "No projects yet. Create your first one to get started." |
| Loading     | Calm, reassuring         | "Loading your dashboard..."                              |
| Warning     | Serious, cautionary      | ⚠️ "This action cannot be undone."                       |

---

## Writing Principles

### 1. Be Specific

```typescript
// ❌ VAGUE: User sees this and thinks "what happened?"
<div>Something went wrong</div>

// ✅ SPECIFIC: User knows exactly what failed
<div>Failed to save profile. Check your connection and try again.</div>
```

---

### 2. Lead with Action

```typescript
// ❌ PASSIVE: Weak call to action
<button>Changes will be saved</button>

// ✅ ACTIVE: Clear user action
<button>Save Changes</button>
```

---

### 3. Keep it Short

```typescript
// ❌ WORDY: Too much text, users won't read it
<p>
  In order to proceed with creating a new project, you will need to
  click on the button below that says "Create Project".
</p>

// ✅ CONCISE: Get to the point
<p>Click "Create Project" to start.</p>

// ✅ BETTER: Remove redundant instruction
<button>Create Project</button>
```

---

### 4. Use Consistent Terminology

```typescript
// ❌ INCONSISTENT: Same concept, different words
<button>Delete Item</button>
<button>Remove Post</button>
<button>Discard Comment</button>

// ✅ CONSISTENT: Pick one term and use it everywhere
<button>Delete Item</button>
<button>Delete Post</button>
<button>Delete Comment</button>
```

**Buzz Stack Terminology:**

| Use This | Not This               |
| -------- | ---------------------- |
| Delete   | Remove, Discard, Erase |
| Create   | Add, New, Make         |
| Save     | Submit, Confirm, Apply |
| Cancel   | Dismiss, Close, Exit   |
| Sign In  | Log In, Login          |
| Sign Out | Log Out, Logout        |

---

## Microcopy Patterns

### Error Messages

**Structure:** [What happened] + [Why it happened] + [What to do next]

```typescript
// ❌ BAD: Vague, no action
<div role="alert">Error occurred</div>

// ❌ BAD: Technical jargon
<div role="alert">ERR_CONNECTION_REFUSED: ECONNREFUSED</div>

// ✅ GOOD: Clear, actionable
<div role="alert">
  Unable to save your changes. Your internet connection appears to be offline.
  Please check your network and try again.
</div>

// ✅ BETTER: Concise, actionable
<div role="alert">
  Couldn't save changes. Check your connection and try again.
</div>
```

**Error Message Template:**

```typescript
interface ErrorMessageProps {
  action: string; // What failed
  reason?: string; // Why it failed (optional)
  solution: string; // What to do
}

function formatError({ action, reason, solution }: ErrorMessageProps): string {
  return [`Couldn't ${action}.`, reason, solution].filter(Boolean).join(" ");
}

// Usage
formatError({
  action: "delete post",
  reason: "Post is already deleted.",
  solution: "Refresh the page.",
});
// Result: "Couldn't delete post. Post is already deleted. Refresh the page."
```

---

### Success Messages

**Keep brief and affirmative:**

```typescript
// ❌ TOO LONG
<div>Your profile has been successfully updated and saved to the database.</div>

// ✅ BRIEF
<div>Profile saved!</div>

// ✅ WITH CONTEXT
<div>Profile updated. Changes are now live.</div>
```

---

### Empty States

**Structure:** [What's missing] + [Why it matters] + [Call to action]

```typescript
// ❌ BAD: Not helpful
<div>No data</div>

// ❌ BAD: States the obvious
<div>You don't have any projects</div>

// ✅ GOOD: Explains + guides
<div>
  <h3>No projects yet</h3>
  <p>Projects help you organize your work.</p>
  <button>Create Your First Project</button>
</div>

// ✅ WITH ICON: More engaging
<div className="text-center p-8">
  <FolderIcon className="w-16 h-16 mx-auto text-gray-400" />
  <h3 className="text-lg font-medium mt-4">No projects yet</h3>
  <p className="text-gray-600 mt-2">
    Create a project to start organizing your work.
  </p>
  <button className="mt-4 px-4 py-2 bg-blue-500 text-white rounded">
    Create Project
  </button>
</div>
```

---

### Loading States

**Be specific about what's loading:**

```typescript
// ❌ GENERIC
<div>Loading...</div>

// ✅ SPECIFIC
<div>Loading your dashboard...</div>

// ✅ WITH PROGRESS (when deterministic)
<div>
  Uploading file... 45%
  <progress value={45} max={100} />
</div>

// ✅ WITH SKELETON (best UX)
<div className="animate-pulse">
  <div className="h-8 bg-gray-200 rounded w-1/2 mb-4"></div>
  <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
  <div className="h-4 bg-gray-200 rounded w-2/3"></div>
</div>
```

---

### Button Labels

**Use verb + noun structure:**

```typescript
// ❌ VAGUE
<button>OK</button>
<button>Submit</button>
<button>Click Here</button>

// ✅ CLEAR
<button>Save Changes</button>
<button>Send Message</button>
<button>Create Account</button>

// ✅ WITH CONTEXT
<button>Delete Post</button>
<button>Cancel Subscription</button>
<button>Download Report</button>
```

---

### Confirmation Dialogs

**Structure:** [Warning/Question] + [Consequences] + [Action Buttons]

```typescript
// ❌ BAD: Unclear consequences
<dialog>
  <p>Are you sure?</p>
  <button>Yes</button>
  <button>No</button>
</dialog>

// ✅ GOOD: Clear consequences + specific actions
<dialog>
  <h3>Delete this post?</h3>
  <p>
    This action cannot be undone. The post will be permanently deleted.
  </p>
  <div className="flex gap-2">
    <button className="bg-red-600 text-white">Delete Post</button>
    <button className="bg-gray-200">Cancel</button>
  </div>
</dialog>

// ✅ BETTER: Echo the action in button
<dialog>
  <h3>Delete "My First Post"?</h3>
  <p>This action cannot be undone.</p>
  <div className="flex gap-2">
    <button className="bg-red-600 text-white">Yes, Delete</button>
    <button className="bg-gray-200">No, Keep It</button>
  </div>
</dialog>
```

---

## Accessibility Considerations

### Screen Reader Text

**Provide context for non-visible elements:**

```typescript
// ❌ BAD: Icon with no label
<button>
  <TrashIcon />
</button>

// ✅ GOOD: Visually hidden label for screen readers
<button>
  <TrashIcon aria-hidden="true" />
  <span className="sr-only">Delete post</span>
</button>

// ✅ ALTERNATIVE: aria-label
<button aria-label="Delete post">
  <TrashIcon />
</button>
```

---

### Dynamic Content Announcements

**Announce changes to screen readers:**

```typescript
// ✅ GOOD: Announce status changes
<div role="status" aria-live="polite">
  {isLoading && "Saving changes..."}
  {isSaved && "Changes saved successfully"}
  {error && `Error: ${error.message}`}
</div>

// ✅ URGENT: Use assertive for errors
<div role="alert" aria-live="assertive">
  {error && `Failed to save. ${error.message}`}
</div>
```

---

### Form Labels & Hints

```typescript
// ❌ BAD: No label
<input placeholder="Email" />

// ✅ GOOD: Explicit label + hint
<div>
  <label htmlFor="email">Email Address</label>
  <input
    id="email"
    type="email"
    aria-describedby="email-hint"
  />
  <span id="email-hint" className="text-sm text-gray-600">
    We'll never share your email.
  </span>
</div>

// ✅ GOOD: Error state
<div>
  <label htmlFor="email">Email Address</label>
  <input
    id="email"
    type="email"
    aria-invalid="true"
    aria-describedby="email-error"
  />
  <span id="email-error" className="text-sm text-red-600" role="alert">
    Please enter a valid email address.
  </span>
</div>
```

---

## Anti-Patterns to Avoid

### ❌ Using Jargon

```typescript
// ❌ TECHNICAL: Developers understand, users don't
<div>ERR_NETWORK_403: CORS policy violation</div>

// ✅ PLAIN LANGUAGE: Everyone understands
<div>Access denied. You don't have permission to view this content.</div>
```

---

### ❌ Being Cute or Clever

```typescript
// ❌ CLEVER: Confusing
<div>Oops! Looks like our hamsters stopped running.</div>
<button>Ruh-roh! Try Again?</button>

// ✅ CLEAR: Direct and helpful
<div>Server error. We're working on it.</div>
<button>Try Again</button>
```

**Exception:** Non-critical 404 pages can have personality.

---

### ❌ Blaming the User

```typescript
// ❌ BLAME: Makes user feel bad
<div>You entered an invalid email address.</div>
<div>Your input is incorrect.</div>

// ✅ NEUTRAL: Focus on the issue, not the user
<div>Please enter a valid email address.</div>
<div>This field is required.</div>
```

---

### ❌ Apologizing Excessively

```typescript
// ❌ OVER-APOLOGETIC: Weakens message
<div>
  We're terribly sorry, but unfortunately we're unable to process your
  request at this time. We apologize for any inconvenience this may cause.
</div>

// ✅ BRIEF: One apology, focus on solution
<div>
  Sorry, we couldn't process your request. Please try again in a few minutes.
</div>
```

---

## Content Checklist

**Before shipping UI copy, verify:**

- [ ] **Specific** - Says what happened and what to do
- [ ] **Actionable** - Includes next steps or solution
- [ ] **Concise** - Uses as few words as possible
- [ ] **Consistent** - Matches terminology across app
- [ ] **Accessible** - Screen reader friendly
- [ ] **Clear** - No jargon or technical terms
- [ ] **Active voice** - "Save changes" not "Changes will be saved"
- [ ] **Positive tone** - Focus on solution, not blame

---

## Real-World Examples

### Search Empty State

```typescript
// Component: SearchResults.tsx
{results.length === 0 && (
  <div className="text-center p-8">
    <SearchIcon className="w-16 h-16 mx-auto text-gray-400" />
    <h3 className="text-lg font-medium mt-4">No results found</h3>
    <p className="text-gray-600 mt-2">
      Try a different search term or clear your filters.
    </p>
    <button
      onClick={clearFilters}
      className="mt-4 px-4 py-2 bg-blue-500 text-white rounded"
    >
      Clear Filters
    </button>
  </div>
)}
```

---

### Form Validation

```typescript
// Component: LoginForm.tsx
{errors.email && (
  <p className="text-sm text-red-600 mt-1" role="alert">
    {errors.email === 'required' && 'Email is required.'}
    {errors.email === 'invalid' && 'Please enter a valid email address.'}
  </p>
)}

{errors.password && (
  <p className="text-sm text-red-600 mt-1" role="alert">
    {errors.password === 'required' && 'Password is required.'}
    {errors.password === 'min_length' && 'Password must be at least 8 characters.'}
  </p>
)}
```

---

## References

- [Customer-Facing Documentation](../../../docs/CUSTOMER-FACING.md) - User guides
- [Accessibility Skill](../accessibility-audits/SKILL.md) - A11y best practices
- [UX/UI Design Skill](../ux-ui-design/SKILL.md) - Visual hierarchy
- [Content Design Patterns](https://design.google/library/content-design-patterns/) - External resource

---

**Skill Status:** Enhanced
**Last Updated:** 2026-02-13 (Phase 5)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
