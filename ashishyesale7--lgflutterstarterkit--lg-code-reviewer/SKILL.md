---
name: lg-code-reviewer
description: Use when working with the final gatekeeper for quality. Use after a feature implementation is finished to ensure OSS standards, performance, and architectural purity.
metadata:
  author: ashishyesale7
---

# The OSS Code Reviewer

## Overview
Fifth step in the pipeline: **Init -> Brainstorm -> Plan -> Execute -> Review -> Quiz (Finale)**. Simulates a professional code review.

**Announce at start:** "I'm starting a professional Code Review for the [Feature Name] implementation."

**⚠️ PROMINENT GUARDRAIL**: The **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md) and **LG Shield** (.agent/skills/lg-shield/SKILL.md) are active at all times. DO NOT auto-fix issues — guide the student to fix them.

## The Review Process

### 1. Holistic Quality Check
- **SOLID Purity**: Are classes doing too much?
- **DRY Compliance**: Any copy-pasted logic that should be shared?
- **Naming**: Descriptive and consistent with Dart conventions?
- **LG-<TaskName> Convention**: Repo name, app title, and README all use the `LG-<TaskName>` pattern?
- **Widget Tree**: Appropriately decomposed? No "God Widgets."
- **Provider Usage**: Services properly registered and consumed?

### 2. Technical Tooling Audit (Mandatory)
Run and verify:
- **Analysis**: `flutter analyze` — zero errors/warnings.
- **Formatting**: `dart format --set-exit-if-changed .`
- **Tests**: `flutter test` — all passing.
- **Coverage**: `flutter test --coverage` — target 80%.

### 3. Liquid Galaxy Specific Audit
- **SSH Lifecycle**: Connections properly opened, verified, and disposed?
- **KML Validity**: Valid XML, correct coordinate ordering (lon, lat, alt)?
- **KML Cleanup**: Old overlays cleared before sending new data?
- **Service Layer**: UI delegates to services, no direct SSH/KML in widgets?
- **Memory Safety**: Dispose methods clean up SSH connections and resources?

### 4. Layer Boundary & Security Audit
- **Import compliance**: No `dartssh2` or `package:http` in `screens/` or `widgets/`.
- **No KML in UI**: Screens must not contain KML string literals or XML generation.
- **No network in UI**: Screens must not call `http.get()` or open sockets.
- **Provider contracts**: API implementations expose only domain models downstream.
- **Secret storage**: No passwords/API keys in `SharedPreferences` — must use `flutter_secure_storage`.
- **Repository hygiene**: `.gitignore` excludes `.env`, keystores, build artifacts.
- See `.agent/rules/layer-boundaries.md` for the full import matrix.

### 5. Documentation & OSS Readiness
- **Dart Doc**: `///` doc comments on all public members.
- **README**: Explains new features, includes setup instructions, screenshots.
- **App name**: Follows `LG-<TaskName>` convention in README, `pubspec.yaml`, and repo name.
- **Readability**: New student could understand in 5 minutes.

### 6. Contest Alignment (Gemini Summer of Code 2026)
- **Mandatory screens**: Splash, Home, Connection Settings, Settings/About, Help (per Lucia's guide).
- **Task 2 operations**: FlyTo, Orbit, SendKML, ClearKML, Logo overlay — all implemented?
- **Reference links**: README links to Lucia's repo, LG LAB, contest page?
- See `.agent/rules/lg-architecture.md` for the full Task 2 requirements table.

## Review Report
Save to `docs/reviews/YYYY-MM-DD-<feature>-review.md`.

```markdown
# Code Review: [Feature Name]

## The Good
- [Strengths]

## Tooling & Quality Status
- **Analysis**: [PASS/FAIL]
- **Formatting**: [PASS/FAIL]
- **Tests**: [PASS/FAIL]
- **Coverage**: [X]% (Target: 80%)

## Required Refactors
- [Must fix before merging]

## Best Practice Suggestions
- [Minor improvements]

## Final Verdict: [APPROVED / REVISIONS NEEDED]
```

## Guardrail: Revision Loop
If REVISIONS NEEDED, hand back to Plan Writer or Executer. Feature is not "Complete" until APPROVED.

## ⛔️ Student Interaction — MANDATORY

**Before issuing the final verdict, STOP and discuss with the student:**
1. Walk through the most significant findings (both good and bad).
2. Ask: *"Which of these issues do you understand? Which do you need help with?"*
3. For any item the student doesn't understand, link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md).

**DO NOT auto-fix issues** — guide the student to fix them, then re-review.

## Reference Links

- **Lucia's LG Master App (reference implementation)**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App
- **Effective Dart style guide**: https://dart.dev/effective-dart
- **Flutter performance best practices**: https://docs.flutter.dev/perf/best-practices
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## Final Completion
Once APPROVED:
- Commit: `chore: final polish after code review`
- Hand to **Quiz Master** (.agent/skills/lg-quiz-master/SKILL.md)

## 🔗 Skill Chain

After the review is APPROVED and the student understands all findings, **automatically offer the next stage**:

> *"Code review APPROVED! Your code is clean, secure, and well-architected. Before we graduate, let's run a Security Post-Flight scan, then it's time for the **Liquid Galaxy Quiz Show** finale! Ready? 🎤"*

If student says "ready" → activate `.agent/skills/lg-shield/SKILL.md` (post-flight mode), then `.agent/skills/lg-quiz-master/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
