---
name: backend-dev
description: Backend delivery skill for APIs, services, databases, and AI integrations with docs-first implementation Use when this capability is needed.
metadata:
  author: sujan-6905
---

# Backend Development Skill

## Use This Skill For

- API endpoints and backend services
- database-backed application logic
- authentication, authorization, validation, and rate limiting
- AI or agent framework integrations on the server side

## Do Not Use This Skill For

- purely visual UI work
- deployment-only changes without backend logic changes

## Default Workflow

1. Confirm the runtime, framework, and database in use.
2. Read the relevant implementation surface before editing.
3. Check official docs with `context7` for framework or SDK behavior.
4. Use `gh_grep` only when a production pattern would reduce guesswork.
5. Implement the smallest coherent backend change.
6. Verify with the strongest available local checks.

## Backend Standards

- Prefer explicit validation at the boundary.
- Return predictable error shapes.
- Handle timeouts, retries, and rate limits intentionally.
- Add health or readiness endpoints when operationally relevant.
- Use structured logging when the project already has a logging path.

## Security Rules

- Validate all external input.
- Sanitize output when rendering or forwarding untrusted data.
- Keep secrets out of source control.
- Never read or edit `.env` directly.
- If configuration changes are needed, create or update `.env.example` and document the variables there.
- Assume the same keys exist in the local `.env`.

## Database Guidance

- Follow the user-specified database if one exists.
- If none is specified, choose the simplest credible option for the use case.
- Make schema changes and application changes together when practical.
- Avoid hidden migration behavior; document or generate the required change explicitly.

## AI and Agent Integrations

- Verify provider SDK versions and capabilities before coding.
- Check auth, quotas, streaming behavior, and error semantics.
- Prefer documented APIs over reverse-engineered behavior.

## Done Criteria

- implementation matches the current stack
- validation and error handling are not omitted
- env requirements are reflected in `.env.example`
- README or setup notes are updated when behavior changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
