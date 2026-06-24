---
name: flutter-ai-app-delivery
description: Use when building, planning, or reviewing Flutter mobile apps with AI features. Covers Flutter app structure, state, local storage, backend AI proxying, streaming, structured output, and MVP delivery.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [flutter, dart, mobile, ai, apps, frontend]
    related_skills: [mobile-app-engineering-roadmap, dotnet-mobile-ai-backend, frontend-accessibility-review]
    created_by: agent
---

# Flutter AI App Delivery

## Overview

Use this skill when the user wants to build a polished cross-platform mobile app with Flutter, especially one that uses AI.

Default architecture:

```text
Flutter app
  -> HTTPS API
  -> backend AI proxy
  -> AI provider
```

The Flutter app owns UI, local state, local cache, and user interactions. The backend owns secrets, AI calls, persistence rules, and usage controls.

## When to Use

Use when:

- Building a Flutter mobile app.
- Choosing screens/state/local storage for a mobile MVP.
- Adding AI features to a Flutter app.
- Reviewing Flutter architecture.
- Building portfolio/mobile app prototypes.

Do not use for Unity games or .NET MAUI apps except as comparison context.

## Project Shape

Recommended Flutter project layout:

```text
lib/
  main.dart
  app.dart
  core/
    config/
    theme/
    routing/
    http/
    errors/
  features/
    quests/
      data/
        quest_api.dart
        quest_repository.dart
        quest_local_store.dart
      domain/
        quest.dart
        quest_generation_request.dart
      presentation/
        quest_list_screen.dart
        quest_detail_screen.dart
        add_quest_screen.dart
        quest_controller.dart
  shared/
    widgets/
    loading.dart
    empty_state.dart
    error_state.dart
```

Keep features vertical. Do not scatter all models, services, and widgets into global folders once the app has more than one feature.

## State Management Defaults

For learning and small/medium apps:

- Use Riverpod if no stack is specified.
- Use simple `StateNotifier`/`AsyncNotifier` patterns.
- Keep API models and UI state separate when complexity grows.

Practical state categories:

| State type | Examples | Storage |
|---|---|---|
| UI state | selected tab, expanded panel | widget/controller |
| Session state | current user, auth token | secure storage/provider |
| Domain state | quests, cards, notes | repository/local DB |
| Request state | loading/error/retry | controller/AsyncValue |

## Local Storage Defaults

Choose the smallest thing that works:

| Need | Tool |
|---|---|
| App preferences | `shared_preferences` |
| Secrets/tokens | `flutter_secure_storage` |
| Small local records | SQLite/Drift or Isar |
| Offline-first relational data | Drift |
| Simple JSON cache | file/storage + repository wrapper |

For serious apps, prefer a repository layer so the UI does not care whether data came from local storage or the network.

## AI Feature Architecture

Never call paid AI providers directly from Flutter with secret keys.

Use:

```text
Flutter
  POST /api/quest/generate
Backend
  validates request
  applies prompt template
  calls AI provider
  validates structured JSON
  returns safe response
```

### Example mobile request

```json
{
  "rawTask": "clean the kitchen",
  "tone": "dark fantasy but practical",
  "difficulty": "small"
}
```

### Example backend response

```json
{
  "title": "Clear the Goblin Kitchen",
  "steps": [
    "Gather dishes",
    "Clear counters",
    "Wipe surfaces"
  ],
  "rewardXp": 25,
  "estimatedMinutes": 15
}
```

## AI UX Rules

AI in mobile should feel fast and bounded.

- Use AI for a specific button/action, not every screen.
- Show loading states with cancel/retry.
- Cache useful outputs.
- Let the user edit AI-generated content.
- Prefer structured output over raw chat text.
- Stream only when the user benefits from seeing progress.
- Have fallback templates if AI fails.

## MVP: AI Quest Log

Good first project for the user:

```text
Screens:
- Home / Today’s quests
- Add Task
- Quest Detail
- History

Backend:
- POST /api/quests/generate

Local data:
- Quest
- QuestStep
- XP total
```

Build order:

1. Static UI with fake data.
2. Local quest create/complete.
3. Backend endpoint returning fake generated quest.
4. Real AI provider call.
5. Structured JSON validation.
6. Polish loading/error/edit states.
7. Run on real device.

## Testing Checklist

- [ ] Unit-test prompt/result parsing on backend.
- [ ] Test repository with fake API/local store.
- [ ] Test critical controllers/state transitions.
- [ ] Manually test small-screen layout.
- [ ] Test no-network behavior.
- [ ] Test AI failure fallback.

## Common Pitfalls

1. **API key in Flutter.** Never do this for paid AI keys.
2. **Building chat first.** A structured action is usually better than generic chat.
3. **No edit path.** AI output should be editable.
4. **Skipping fake data.** Build UI with fake data before API complexity.
5. **Over-architecting.** Use a clean feature folder; do not recreate enterprise architecture on app one.
6. **Ignoring cost.** Backend should log usage and cap abusive calls.

## Verification Checklist

- [ ] App has a clear feature-folder structure.
- [ ] State management choice is explicit.
- [ ] Local persistence choice is explicit.
- [ ] AI calls go through a backend proxy.
- [ ] Structured AI output is validated before display.
- [ ] Loading/error/offline states are handled.
- [ ] MVP can be run on emulator or real device.

---
> Source: [Stormxftw/practical-hermes-skills](https://github.com/Stormxftw/practical-hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
