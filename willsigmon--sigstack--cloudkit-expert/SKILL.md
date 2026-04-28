---
name: cloudkit-expert
description: CloudKit sync - schema design, conflict resolution, offline support, multi-device sync validation Use when this capability is needed.
metadata:
  author: willsigmon
---

# CloudKit Expert

CloudKit architecture for Leavn's multi-device sync.

## Core Concepts
- **Containers**: App's CloudKit namespace
- **Databases**: Private (user), Public (shared), Shared (collaboration)
- **Record Types**: Schema definitions
- **Zones**: Custom zones for atomic operations

## Sync Architecture
1. Local SwiftData/CoreData as source of truth
2. CloudKit as sync transport
3. CKSyncEngine for automatic sync (iOS 17+)

## Conflict Resolution
- **Last-write-wins**: Simple, may lose data
- **Field-level merge**: Merge non-conflicting fields
- **Custom resolver**: App-specific logic

## Offline Support
- Queue changes locally when offline
- Sync when connectivity restored
- Handle partial sync failures

## Validation Checklist
- [ ] Schema matches local model
- [ ] Indexes on query fields
- [ ] Conflict handling tested
- [ ] Offline → online sync works
- [ ] Delete propagation correct

Use when: CloudKit bugs, sync issues, schema design, conflict resolution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsigmon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
