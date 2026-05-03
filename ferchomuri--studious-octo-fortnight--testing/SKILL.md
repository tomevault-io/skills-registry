---
name: unit-testing
description: Guidelines and standards for writing unit tests in Next.js using Jest. Use when this capability is needed.
metadata:
  author: ferchomuri
---

# Agents Guidelines – Unit Testing for Next.js with Jest

## 🎯 Purpose

This project uses Google Antigravity as an assistant agent.These guidelines define how unit tests must be written for a Next.js application using Jest.

The goal is to keep tests:

- Simple
- Readable
- Focused on a single component
- Free of unnecessary logic or complexity

## 🧠 Testing Philosophy

- Test observable behavior, not implementation details
- One test should answer one clear question
- Anything external to the component must be mocked
- The simpler the test, the better
- If a test feels complex, it is probably wrong

## 📦 Testing Scope

### ✅ What should be tested

- Component rendering
- Presence of important text, labels, or titles
- Visible states such as loading, empty, or error
- Visual changes caused by props
- Simple internal interactions like clicks, toggles, or conditional rendering

### ❌ What should NOT be tested

- Real API calls
- External services logic
- Third-party libraries such as charts, maps, or analytics
- Internal implementations of external hooks
- Integration between multiple components

## 🧪 Mocking Strategy in Next.js

Everything that is not owned directly by the component must be mocked.

This includes:

- Data fetching functions
- Custom hooks
- External UI libraries
- Chart and visualization tools
- Context providers
- Next.js navigation and routing utilities

Mocks should remain static, simple, and easy to understand.

## 📊 Charts and Complex UI Components

For charts or visually complex components:

- Do not test the chart library itself
- Do not validate internal chart structures
- Verify a simple visible signal instead

Valid signals include:

- A specific label
- A visible title
- A legend text
- A data-testid rendered by the component

The test should only confirm that the component renders the expected visual cue.

## 🧩 Characteristics of a Good Unit Test

A good unit test is:

- Small and focused
- Easy to read
- Free of conditional logic
- Focused on what the user can see or interact with

If someone new to the project can understand the test in seconds, it is a good test.

## 🧱 Recommended Structure for Next.js

- One test file per component
- Clear and consistent naming
- Tests located close to the component or inside a dedicated tests folder

The structure should favor discoverability and simplicity.

## 🚫 Important Restrictions

- Do not create unnecessary test helpers
- Do not abstract test logic prematurely
- Do not use complex or dynamic mocks
- Do not test multiple components in a single test file
- Do not add conditional logic inside tests

## 🧠 Golden Rule

If the test is more complex than the component, the test is wrong.

The purpose of unit testing is fast and clear confidence,not artificial coverage.

## ✅ Pre-Commit Checklist

Before approving or merging a test, confirm that:

- It tests only one component
- Everything external is mocked
- The test is easy to read
- No unnecessary logic is present
- The test can be understood in under 30 seconds

If all answers are yes, the test meets the standard.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferchomuri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
