---
name: ooui-design
description: Object-Oriented UI (OOUI) design specialist applying HIG principles. Use when designing information architecture, navigation, screen flows, or restructuring task-oriented UI into object-oriented patterns. Transforms "verb-first" interfaces into intuitive "noun-first" experiences. Use when this capability is needed.
metadata:
  author: ayatoashihara
---

# Object-Oriented UI Design

Design interfaces that users understand intuitively by putting objects first.

## Scope

**Use for:** Information architecture, navigation design, screen flow design, UI restructuring, dashboard design, admin panels, CRUD interfaces, data-heavy applications.

**Not for:** Visual styling, color palettes, animations (use `/interface-design` or `/ui-ux-pro-max`).

---

# The Core Problem

Most interfaces are **task-oriented**: they organize around what users can DO.

```
❌ Task-Oriented (Verb First)
Menu: [New] [Edit] [Delete] [Export] [Import]
      ↓
User: "What do I click to edit my invoice?"
```

This forces users to think in system terms. They must know the vocabulary. They must guess which verb applies to which noun.

**Object-oriented interfaces organize around what users can SEE.**

```
✅ Object-Oriented (Noun First)
Invoices → Invoice #1234 → [Edit] [Delete] [Export]
           ↑ select first, then act
```

Users find the thing, then choose what to do with it. Natural. Intuitive. No guessing.

---

# The OOUI Principle

**"Noun → Verb" not "Verb → Noun"**

| Task-Oriented | Object-Oriented |
|---------------|-----------------|
| "Create Invoice" button | Invoice list → [+ New] |
| "Delete" menu item | Select invoice → [Delete] |
| Modal forms | Inline editing on object |
| Wizard flows | Object with states |
| Feature menus | Object actions |

**The test:** Can users see the objects before they act? If the interface requires users to choose an action before seeing what they're acting on, it's task-oriented.

---

# Objects, Views, Actions

## 1. Identify Objects

Objects are the **nouns** users care about. Not features. Not screens. Things.

**Questions to find objects:**
- What does the user want to see?
- What do they want to create, edit, delete?
- What do they talk about in their own words?

**Example - Project Management App:**
```
Objects: Project, Task, Team Member, Comment, File, Milestone
NOT: Dashboard, Settings, Reports (these are views, not objects)
```

**Example - E-commerce Admin:**
```
Objects: Product, Order, Customer, Category, Coupon, Review
NOT: Analytics, Configuration, Management (abstract, not objects)
```

## 2. Define Object Properties

Each object has:

| Property | Description | Example (Invoice) |
|----------|-------------|-------------------|
| **Identity** | What makes it unique | Invoice #1234 |
| **Attributes** | Descriptive data | Amount, Date, Status |
| **State** | Current condition | Draft, Sent, Paid, Overdue |
| **Relationships** | Connected objects | Customer, Line Items |

## 3. Design Two Views Per Object

Every object needs exactly two views:

### Collection View (List)
Shows multiple objects. Enables browse, search, filter, sort.

```
┌─────────────────────────────────────────────────┐
│ 📋 Invoices                        [+ New]      │
├─────────────────────────────────────────────────┤
│ [Search...] [Status ▼] [Date ▼]                 │
├─────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────┐ │
│ │ #1234  Acme Corp     $5,000    ● Paid       │ │
│ │ #1235  Beta Inc      $3,200    ○ Sent       │ │
│ │ #1236  Gamma LLC     $1,800    ◐ Draft      │ │
│ └─────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
```

**Collection view must show:**
- Object identity (name, ID)
- Key attributes (2-4 most important)
- Current state (visual indicator)
- Quick actions (optional, on hover)

### Singleton View (Detail)
Shows one object. Enables deep inspection and manipulation.

```
┌─────────────────────────────────────────────────┐
│ ← Invoices          #1234            [Actions ▼]│
├─────────────────────────────────────────────────┤
│                                                 │
│ Acme Corporation                    ● Paid      │
│ ───────────────────────────────────────────     │
│                                                 │
│ Amount:     $5,000.00                           │
│ Issued:     2024-01-15                          │
│ Due:        2024-02-15                          │
│ Paid:       2024-02-10                          │
│                                                 │
│ Line Items                                      │
│ ┌─────────────────────────────────────────────┐ │
│ │ Consulting Services    40h × $100   $4,000  │ │
│ │ Travel Expenses                     $1,000  │ │
│ └─────────────────────────────────────────────┘ │
│                                                 │
│ Related                                         │
│ 👤 Customer: Acme Corp                          │
│ 📎 Attachments: 2 files                         │
└─────────────────────────────────────────────────┘
```

**Singleton view must show:**
- Full identity
- All attributes (organized by importance)
- Current state (prominent)
- Related objects (links to their views)
- Available actions (contextual)

## 4. Place Actions on Objects

Actions belong TO objects, not in menus.

**Placement rules:**

| Action Type | Location |
|-------------|----------|
| Create new | Collection view header |
| Quick actions | Object row (on hover) |
| Full actions | Singleton view header |
| Bulk actions | Collection view (when selected) |
| Destructive | Confirmation required |

```
Collection: [+ New Invoice]
            Invoice row → [⋮] → Edit, Duplicate, Delete
            
Singleton:  [Edit] [Send] [Download PDF] [⋮ More]
```

---

# Navigation Architecture

## Sidebar = Object Types

The primary navigation lists **object types**, not features.

```
┌─────────┐
│ 📊 Home │ ← Dashboard (special: aggregates objects)
│         │
│ 📋 Tasks│ ← Object type
│ 📁 Proj │ ← Object type
│ 👥 Team │ ← Object type
│ 📎 Files│ ← Object type
│         │
│ ⚙️ Set  │ ← Settings (special: system config)
└─────────┘
```

**NOT this:**
```
❌ 
│ ➕ Create  │ ← Verb (task-oriented)
│ 📝 Edit    │ ← Verb
│ 📊 Reports │ ← Abstract feature
│ 🔧 Tools   │ ← Vague category
```

## Breadcrumbs = Object Path

Show the user's location in object space.

```
Home → Projects → Project Alpha → Tasks → Task #42
       ↑ collection   ↑ singleton    ↑ collection  ↑ singleton
```

## URL = Object Address

URLs should reflect object hierarchy.

```
/projects                    → Collection (all projects)
/projects/alpha              → Singleton (Project Alpha)
/projects/alpha/tasks        → Collection (tasks in Alpha)
/projects/alpha/tasks/42     → Singleton (Task #42)
```

---

# Common Patterns

## Master-Detail

Collection and singleton side by side.

```
┌───────────────────┬─────────────────────────────┐
│ 📋 Messages       │ Message from John           │
├───────────────────┤                             │
│ ● John: Hey...    │ Hey, are we still on for    │
│   Sarah: Update   │ tomorrow's meeting?         │
│   Mike: Question  │                             │
│                   │ [Reply] [Archive] [Delete]  │
└───────────────────┴─────────────────────────────┘
```

**Use when:** Objects are frequently browsed, detail fits alongside list.

## Drill-Down

Collection → Singleton as separate pages.

```
Page 1: Invoice List
        Click → 
Page 2: Invoice #1234 Detail
```

**Use when:** Singleton needs full attention, mobile-first.

## Inline Editing

Edit directly in the view, no separate mode.

```
┌─────────────────────────────────────┐
│ Task: [Fix login bug        ✎ ]    │  ← Click to edit
│ Status: [In Progress ▼]            │  ← Dropdown
│ Due: [2024-02-01 📅]               │  ← Date picker
└─────────────────────────────────────┘
```

**Use when:** Edits are frequent, fields are independent.

## Modal for Create

New object creation in focused modal.

```
Collection: [+ New Invoice] → Opens modal
Modal: Form for new invoice → [Create]
Result: New invoice in collection, optionally open singleton
```

**Use when:** Creation requires focus, multiple fields.

---

# Relationship Patterns

## One-to-Many (Parent-Child)

Show children within parent's singleton view.

```
Project Alpha (singleton)
├── Tasks (embedded collection)
│   ├── Task 1
│   ├── Task 2
│   └── [+ Add Task]
└── Files (embedded collection)
    ├── spec.pdf
    └── [+ Upload]
```

## Many-to-Many

Show as related objects with links.

```
Task #42 (singleton)
├── Assigned to: [👤 John] [👤 Sarah] [+ Assign]
└── Tags: [🏷️ Bug] [🏷️ Urgent] [+ Tag]

Clicking [👤 John] → John's singleton view
```

## Aggregations (Dashboard)

Dashboard is a **special view** that aggregates objects.

```
┌─────────────────────────────────────────────────┐
│ 📊 Dashboard                                    │
├─────────────────────────────────────────────────┤
│                                                 │
│ My Tasks (3 urgent)        → link to Tasks      │
│ ├── Fix login bug                               │
│ ├── Review PR #45                               │
│ └── [View all tasks →]                          │
│                                                 │
│ Recent Projects            → link to Projects   │
│ ├── Project Alpha                               │
│ └── [View all projects →]                       │
│                                                 │
└─────────────────────────────────────────────────┘
```

Dashboard is NOT the primary way to access objects. It's a shortcut.

---

# HIG Principles (Integrated)

## 1. Mental Model Alignment

Users have expectations. Match them.

| User Thinks | Interface Should |
|-------------|------------------|
| "I want to see my invoices" | Show invoice collection immediately |
| "I want to edit this invoice" | Actions appear ON the invoice |
| "I want to create an invoice" | [+ New] button on invoice collection |

## 2. Direct Manipulation

Objects should feel tangible.

- **Drag to reorder** lists
- **Click to select** before acting
- **Hover to reveal** actions
- **Inline edit** without mode switch

## 3. Immediate Feedback

Every action responds instantly.

| Action | Feedback |
|--------|----------|
| Click object | Visual selection (highlight) |
| Save | Toast "Saved" + visual confirmation |
| Delete | Item removal + undo option |
| Error | Inline message on affected field |

## 4. Undo & Redo

Destructive actions must be reversible.

```
[Delete Invoice]
    ↓
Toast: "Invoice deleted. [Undo]"
    ↓
Click Undo → Invoice restored
```

## 5. Consistent Vocabulary

Use the same words everywhere.

```
✅ "Invoice" everywhere
❌ "Invoice" in nav, "Bill" in form, "Document" in toast
```

---

# Anti-Patterns

## ❌ Verb-First Navigation

```
Bad:  [Create] [Edit] [Delete] [Export]
Good: Objects → Select → [Actions]
```

## ❌ Wizard for Everything

```
Bad:  Step 1 → Step 2 → Step 3 → Step 4 → Done
Good: Object with states, edit any field anytime
```

## ❌ Settings Overload

```
Bad:  Settings page with 50 options
Good: Settings in context (e.g., "Notification: [On ▼]" on object)
```

## ❌ Modal Mania

```
Bad:  Every action opens a modal
Good: Inline actions, modal only for creation/confirmation
```

## ❌ Hidden Objects

```
Bad:  Objects buried in menus or features
Good: Objects visible in primary navigation
```

---

# OOUI Design Process

## Step 1: Extract Objects

List all nouns users care about.

```
"What things does the user want to see, create, edit, delete?"
```

## Step 2: Map Relationships

Draw how objects connect.

```
Customer ──1:N──► Order ──1:N──► Line Item
                    │
                    └──N:1──► Product
```

## Step 3: Design Views

For each object: Collection view + Singleton view.

## Step 4: Place Actions

Attach actions to objects, not menus.

## Step 5: Build Navigation

Sidebar = Object types, URL = Object path.

## Step 6: Validate

**Checklist:**
- [ ] Can users see objects before acting?
- [ ] Are actions attached to objects?
- [ ] Does navigation show object types?
- [ ] Do URLs reflect object hierarchy?
- [ ] Is every interaction reversible?

---

# Commands

- `/ooui:analyze` — Analyze current UI for OOUI compliance
- `/ooui:extract` — Extract objects from requirements
- `/ooui:redesign` — Propose OOUI restructure for task-oriented UI
- `/ooui:validate` — Check design against OOUI principles

---

# Quick Reference

```
OOUI Formula:
  Objects → Views → Actions → Navigation

Object has:
  Identity + Attributes + State + Relationships

Views:
  Collection (list) + Singleton (detail)

Actions:
  Create: Collection header
  Quick: Object row hover
  Full: Singleton header

Navigation:
  Sidebar: Object types
  Breadcrumb: Object path
  URL: /type/id/type/id

Anti-patterns:
  ❌ Verb-first menus
  ❌ Wizards for editing
  ❌ Modals everywhere
  ❌ Hidden objects
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayatoashihara) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
