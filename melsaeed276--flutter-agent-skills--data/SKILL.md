---
name: flutter-data
description: Flutter data layer skill hub: networking (Dio/interceptors/pagination/token refresh), serialization/mappers, and local storage (Hive/SQLite/caching). Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Data Skills

## What this domain covers

Networking, serialization, and local storage. Emphasis on boundaries: DTOs, mappers, repositories, caching, and token refresh.

## When to read it

- You’re integrating a new API or local persistence.
- You’re seeing inconsistent parsing, pagination bugs, or auth refresh loops.

## Subtopics

- Networking:
  - [networking/dio.md](./networking/dio.md)
  - [networking/interceptors.md](./networking/interceptors.md)
  - [networking/pagination.md](./networking/pagination.md)
  - [networking/token_refresh.md](./networking/token_refresh.md)
- Serialization:
  - [serialization/json_parsing.md](./serialization/json_parsing.md)
  - [serialization/mappers.md](./serialization/mappers.md)
- Local storage:
  - [local_storage/hive.md](./local_storage/hive.md)
  - [local_storage/sqlite.md](./local_storage/sqlite.md)
  - [local_storage/caching_strategies.md](./local_storage/caching_strategies.md)

## Decision guide

- If you need **request/response policies**, go to [networking/interceptors.md](./networking/interceptors.md).
- If you need **infinite scrolling**, go to [networking/pagination.md](./networking/pagination.md).
- If you need **auth refresh**, go to [networking/token_refresh.md](./networking/token_refresh.md).
- If you need **safe parsing + boundaries**, go to [serialization/mappers.md](./serialization/mappers.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
