---
name: zendesk
description: Manage Zendesk tickets, users, and support workflows through the Zendesk API. Use when searching tickets, updating support state, checking users, or exporting queue data. Use when this capability is needed.
metadata:
  author: jdrhyne
---

# Zendesk

Manage Zendesk tickets, users, and support workflows through the authenticated Zendesk API.

## When to Use

- search tickets or support history
- create or update tickets
- inspect user details
- export queue data for analysis
- summarize current support state

## Setup

Use these environment variables:

- `ZENDESK_SUBDOMAIN`
- `ZENDESK_EMAIL`
- `ZENDESK_TOKEN`

Build the Zendesk auth context from those variables and confirm access before trying ticket operations.

## Workflow Rules

1. Search before creating a ticket to avoid duplicates.
2. Use views or targeted search instead of listing entire queues.
3. Add internal notes when changing status or ownership.
4. Confirm destructive or customer-visible actions before sending them.
5. Respect Zendesk rate limits during bulk work.

## Common Operations

- Search tickets by status, priority, assignee, or subject before creating a new one.
- Create tickets with a clear subject, customer-visible comment, and correct priority.
- Update status with an internal note that explains what changed and why.
- Look up users by email before editing ownership, organization, or requester fields.
- Export queue data only when the user explicitly asked for a saved report.

## Safety Boundaries

- Do not read credentials from ad hoc files, memory stores, or chat history; use only the documented environment variables.
- Do not close, merge, delete, or publicly reply to tickets without explicit confirmation.
- Do not export ticket or user data to files unless the user asked for a saved artifact.
- Do not send Zendesk data to any service other than the authenticated Zendesk API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jdrhyne) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
