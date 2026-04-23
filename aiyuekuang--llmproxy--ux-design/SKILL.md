---
name: ux-design
description: User experience design principles. Use when designing user flows, improving usability, conducting UX reviews, and optimizing user journeys. Use when this capability is needed.
metadata:
  author: aiyuekuang
---

# UX Design Skill

User experience principles for creating intuitive, user-centered interfaces.

## When to Use This Skill

- Designing user flows
- Improving usability
- Conducting UX audits
- Reducing friction points
- Enhancing user journeys

---

# 🎯 UX Principles

## Nielsen's 10 Usability Heuristics

1. **Visibility of system status**
   - Show loading indicators
   - Confirm actions completed
   - Display progress for long operations

2. **Match between system and real world**
   - Use familiar language
   - Follow real-world conventions
   - Avoid technical jargon

3. **User control and freedom**
   - Provide undo/redo
   - Allow cancel actions
   - Easy navigation back

4. **Consistency and standards**
   - Follow platform conventions
   - Consistent button styles
   - Predictable behavior

5. **Error prevention**
   - Validate before submit
   - Confirmation for destructive actions
   - Smart defaults

6. **Recognition rather than recall**
   - Show options visibly
   - Provide context and hints
   - Recent items/history

7. **Flexibility and efficiency**
   - Keyboard shortcuts
   - Customizable workflows
   - Expert shortcuts

8. **Aesthetic and minimalist design**
   - Remove unnecessary elements
   - Focus on essential content
   - White space usage

9. **Help users recognize and recover from errors**
   - Clear error messages
   - Suggest solutions
   - Don't blame the user

10. **Help and documentation**
    - Easy to search
    - Context-sensitive help
    - Task-oriented

---

# 🔄 User Flow Design

## Flow Structure

```
Entry Point → Task Steps → Confirmation → Success State
     ↓
Error Handling → Recovery Path
```

## Sign Up Flow Example

```
Landing Page
    ↓
Email Input → Validation
    ↓
Password Create → Strength Check
    ↓
Profile Setup (Optional) → Skip Option
    ↓
Welcome / Onboarding
    ↓
Main Dashboard
```

## Reduce Steps

```
❌ Bad: 5+ step registration
   Email → Password → Name → Phone → Address → Verify

✅ Good: Progressive disclosure
   Email + Password → Verify → (Collect more later)
```

---

# 📝 Forms UX

## Best Practices

```tsx
// ✅ Good: Clear labels, inline validation
<form>
  <label htmlFor="email">Email address</label>
  <input 
    id="email" 
    type="email"
    placeholder="you@example.com"
    aria-describedby="email-error"
  />
  {error && <span id="email-error" className="text-red-500">{error}</span>}
</form>
```

## Form Guidelines

| Do | Don't |
|----|-------|
| Use single column | Multiple columns on mobile |
| Show password toggle | Force complex requirements |
| Inline validation | Validate only on submit |
| Smart defaults | Make user choose everything |
| Save progress | Lose data on navigation |
| Show required fields | Hide what's optional |

## Error Messages

```
❌ Bad: "Invalid input"
✅ Good: "Please enter a valid email address (e.g., name@example.com)"

❌ Bad: "Error 500"
✅ Good: "Unable to save. Please try again or contact support."
```

---

# ⚡ Reduce Friction

## Friction Points

| Friction | Solution |
|----------|----------|
| Too many clicks | Reduce steps, shortcuts |
| Waiting | Skeleton loading, optimistic UI |
| Confusion | Clear labels, tooltips |
| Errors | Validation, auto-correct |
| Decision fatigue | Smart defaults, recommendations |

## Optimistic UI

```tsx
// Show success immediately, rollback if fails
function handleLike() {
  setLiked(true);  // Instant feedback
  
  api.like(postId).catch(() => {
    setLiked(false);  // Rollback on error
    toast.error('Failed to like');
  });
}
```

---

# 📱 Mobile UX

## Touch Targets

- Minimum: 44x44px
- Comfortable: 48x48px
- Spacing between targets: 8px+

## Thumb Zone

```
┌────────────────────┐
│   Hard to reach    │  ← Move important actions down
│                    │
│  Natural reach     │  ← Primary navigation here
│                    │
│  Easy access       │  ← Main CTAs, bottom nav
└────────────────────┘
```

## Mobile Patterns

- Bottom navigation (5 items max)
- Pull to refresh
- Swipe actions
- Floating action button
- Modal sheets from bottom

---

# 🔔 Feedback & States

## Every Action Needs Feedback

```
Click button    → Visual press state
Submit form     → Loading indicator
Success         → Confirmation message
Error           → Clear error + solution
Empty state     → Helpful guidance
```

## State Hierarchy

```
1. Empty    → "No items yet. Create your first..."
2. Loading  → Skeleton or spinner
3. Error    → Error message + retry
4. Partial  → Show what's available
5. Success  → Full content
```

---

# 🧭 Navigation

## Navigation Types

| Type | Use Case |
|------|----------|
| Top nav | Marketing sites, simple apps |
| Side nav | Dashboards, complex apps |
| Bottom nav | Mobile apps (5 items max) |
| Breadcrumbs | Deep hierarchies |
| Tabs | Related content sections |

## Navigation Guidelines

- Current location always visible
- Back button works as expected
- Consistent across app
- Maximum 7±2 items per level
- Search for large sites

---

# ♿ Accessibility (a11y)

## Quick Checklist

- [ ] Keyboard navigation works
- [ ] Focus states visible
- [ ] Color contrast 4.5:1+
- [ ] Alt text on images
- [ ] Form labels linked
- [ ] Error messages announced
- [ ] Touch targets 44px+
- [ ] No motion for vestibular issues

---

# 📚 UX Resources

- [Nielsen Norman Group](https://www.nngroup.com/)
- [Laws of UX](https://lawsofux.com/)
- [ibelick/ui-skills](https://github.com/ibelick/ui-skills)
- [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiyuekuang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
