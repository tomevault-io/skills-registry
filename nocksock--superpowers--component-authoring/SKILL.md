---
name: component-authoring
description: Use this when writing any kind of UI components in any language (heex, react, web components, and so on). Helps with making them composable and flexible.
metadata:
  author: nocksock
---

# Core Principles for Using LLMs in Component & API Design

## 1) Optimize for *intent*, not implementation

Ask the LLM to reason about **what the component means**, not how it is rendered.

* Names should express *behavior and semantics* (`watch-toggle`, not `watch-button`)
* APIs should describe *what changes*, not *what is clicked*
* Prefer verbs and domain language over UI nouns

> Prompt framing tip:
> “What is the intent of this component?” yields better results than “What should I call this button?”

---

## 2) Explicit ownership beats implicit convention

Always clarify **who owns what**:

* Which component owns layout?
* Which component owns state?
* Which component owns slot placement?

Ambiguity causes LLMs (and humans) to drift into inconsistent abstractions.

> Good LLM prompts explicitly state:
> “Component X owns layout; Component Y owns behavior; slots belong to X.”

---

## 3) Slots are for authored content; attributes are for state & configuration

This separation consistently produced the cleanest outcomes.

**Use slots when:**

* Content may contain markup
* Text must be localized
* Consumers may want icons, emphasis, or structure

**Use attributes/properties when:**

* Representing state (`pressed`, `disabled`)
* Controlling behavior (`mode`, `variant`)
* Passing data used for computation (`price`, `base-price`)

> LLMs reason best when this boundary is explicit in the prompt.

---

## 4) Avoid ambiguous sources of truth

A recurring risk: mixing display text with computational inputs.

**Rule:**
If a component *computes*, its inputs must be unambiguous.

* Numbers → attributes / properties
* Formatting → component responsibility or clearly scoped slots
* Slots override behavior only when explicitly designed to do so

> When asking an LLM, explicitly ask:
> “What is the single source of truth here?”

---

## 5) Prefer controlled or controllable components

Even if state is “internal” today, design for:

* external updates
* synchronization
* deterministic rendering

Expose:

* a reflected state attribute
* an event that expresses intent (`change`, `toggle`)
* tolerance for external state driving

> LLMs tend to default to “self-contained”; steer them by stating future integration needs.

---

## 6) Default behavior + escape hatches

A strong pattern that emerged:

* Provide **sensible defaults** (implicit icon, default layout)
* Keep **explicit escape hatches** (slots, parts, CSS vars)

This avoids both over-configuration and dead-end APIs.

> Ask LLMs:
> “What’s the default 80% case, and what’s the minimal escape hatch?”

---

## 7) Composition over configuration

Your examples worked because composition stayed in markup, not attributes.

Prefer:

```html
<component>
  <icon></icon>
  <label></label>
</component>
```

over:

```html
<component icon="plus" label="Add"></component>
```

LLMs give better architectural guidance when composition is treated as the primary tool.

---

## 8) Shallow hierarchies, small primitives

Avoid deep nesting unless there is:

* semantic meaning
* coordination logic
* reuse outside the immediate context

LLMs are good at spotting when a component is doing “too much” if you ask them to justify each layer’s responsibility.

---

## 9) Events express *intent*, not mechanics

Events should describe **what happened**, not **how**.

* `change`, `toggle`, `submit` → good
* `buttonClicked`, `iconPressed` → leaky abstractions

Ask the LLM to name events from the *consumer’s* perspective.

---

## 10) Make constraints explicit early

This conversation improved sharply once you stated:

* “Option A”
* “Stage owns overlay slots”
* “State is client-stored”
* “Icon may be implicit”

LLMs perform best when constraints are declared up front.

> Good habit:
> Start prompts with a short “Constraints & Assumptions” list.

---

## 11) Treat the LLM as a design reviewer, not an author

The most productive dynamic here was:

* you propose
* the LLM tightens, challenges, and systematizes

Use LLMs to:

* surface ambiguity
* pressure-test APIs
* extract principles after the fact

Not to “invent” architecture in a vacuum.

---

## 12) Extract reusable principles at the end

The final step—asking for principles—is key.

LLMs are especially strong at:

* abstraction
* pattern extraction
* turning concrete discussion into reusable guidance

Make this a standard closing move in design-heavy sessions.

---

### Meta-guideline

If a design decision can’t be cleanly explained in:

* **ownership**
* **source of truth**
* **composition vs configuration**

…it probably isn’t finished yet.

## Core Principles of Componentisation

A quick checklist:

1. Use composition over configuration via attributes by default.
2. A component should only combine layout, state, or behavior when those concerns cannot be meaningfully reused on their own.
3. Data or state must come from exactly one clearly defined input, never from multiple competing sources.
4. Slots are for authored content; attributes are for state and configuration.
5. When a component manages any state, it must be controllable externally.
6. Anticipate most common use-cases and make those the default configuration, but always include clear escape hatches for customization.
7. Name components after intent and behavior, not visual presentation.
8. Emit events that express intent, not implementation details.
9. Keep component hierarchies shallow and primitives narrowly scoped.
10. State constraints and assumptions explicitly before making design decisions.

> [!NOTE]
> If a component’s design cannot be explained using ownership, source of truth, and composition vs configuration, you don’t understand it well enough.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nocksock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
