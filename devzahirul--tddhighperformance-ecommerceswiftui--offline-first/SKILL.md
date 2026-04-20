---
name: offline-first
description: Offline-first patterns with Outbox, optimistic updates, and sync strategies Use when this capability is needed.
metadata:
  author: devzahirul
---

# Offline-First Skill

## Overview
Patterns for building offline-capable iOS apps with optimistic UI, reliable sync, and conflict resolution.

## Core Concept

```
User Action → Local Update (instant) → Queue Operation → Background Sync → Reconcile
```

## Optimistic Updates

### Pattern

```swift
public func addItem(owner: CartOwner, sku: String, qty: Int) async {
    // 1) Optimistic local write - UI updates INSTANTLY
    var cart = await store.get(owner: owner)
    cart.items.append(CartItem(sku: sku, qty: qty))
    cart.version += 1
    await store.upsert(cart)
    
    // 2) Enqueue for background sync
    await outbox.enqueue(CartOp(
        owner: owner,
        type: .addItem(sku: sku, qty: qty)
    ))
}
```

### Benefits
- Instant UI feedback
- Works offline
- Server state eventually consistent

## Outbox Pattern

### Why Outbox?

```
User taps "Pay" → Network timeout → What happened?

❌ Without Outbox: User retries → Double charge!
✅ With Outbox + Idempotency Key: Safe retry, no duplicate
```

### Outbox Store

```swift
public actor OutboxStore {
    private var operations: [CartOp] = []
    
    public func enqueue(_ op: CartOp) {
        operations.append(op)
    }
    
    public func pending() -> [CartOp] {
        operations.filter { $0.nextRetryAt <= Date() }
    }
    
    public func markDone(opId: UUID) {
        operations.removeAll { $0.id == opId }
    }
    
    public func reschedule(opId: UUID, backoffSeconds: Double) {
        if let index = operations.firstIndex(where: { $0.id == opId }) {
            operations[index].attempts += 1
            operations[index].nextRetryAt = Date().addingTimeInterval(backoffSeconds)
        }
    }
}
```

### Idempotency Keys

```swift
public struct CartOp: Identifiable {
    public let id: UUID           // Server uses this to deduplicate
    public let owner: CartOwner
    public let type: CartOpType
    public var attempts: Int = 0
    public var nextRetryAt: Date = Date()
}
```

## Background Sync Worker

```swift
public actor CartSyncWorker {
    private var task: Task<Void, Never>?
    
    public func start() {
        task = Task {
            while !Task.isCancelled {
                for op in await outbox.pending() {
                    await process(op)
                }
                try? await Task.sleep(nanoseconds: 1_000_000_000)
            }
        }
    }
    
    private func process(_ op: CartOp) async {
        do {
            let cart = try await api.addItem(owner: op.owner, opId: op.id, ...)
            await repo.reconcileFromServer(cart)
            await outbox.markDone(opId: op.id)
        } catch {
            // Exponential backoff
            let backoff = min(pow(2, Double(op.attempts + 1)) * 0.6, 8.0)
            await outbox.reschedule(opId: op.id, backoffSeconds: backoff)
        }
    }
}
```

## Server Reconciliation

```swift
public func reconcileFromServer(_ serverCart: Cart) async {
    // Server is source of truth after successful sync
    await store.upsert(serverCart)
}
```

## Conflict Resolution

### Cart Merge (Guest → Authenticated)

```swift
private func handleTransition(from: SessionState?, to: SessionState) async {
    guard case .guest(let anonId) = from,
          case .authenticated(let userId, _) = to else { return }
    
    // 1. Pause sync
    await syncWorker.stop()
    
    // 2. Server-side merge
    let result = try await cartAPI.mergeCart(anonymousId: anonId, userId: userId)
    
    // 3. Reconcile local state
    await cartRepo.reconcileFromServer(result.mergedCart)
    
    // 4. Notify user of adjustments
    await events.pushBanner("Cart merged: \(result.adjustedSkus)")
    
    // 5. Resume sync
    await syncWorker.start()
}
```

## Key Principles

| Principle | Implementation |
|-----------|---------------|
| Local-first | Write to store before network |
| Idempotent | UUID-based operation keys |
| Eventual consistency | Reconcile from server |
| Exponential backoff | Prevent thundering herd |
| Graceful degradation | Work offline, sync when online |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devzahirul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
