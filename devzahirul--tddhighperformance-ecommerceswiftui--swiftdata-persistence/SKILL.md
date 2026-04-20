---
name: swiftdata-persistence
description: SwiftData persistence patterns for iOS apps with @Model and SwiftData containers Use when this capability is needed.
metadata:
  author: devzahirul
---

# SwiftData Persistence Skill

## Overview
SwiftData persistence patterns for iOS apps, enabling durable local storage that survives app restarts.

## SwiftData Models

### Persisted Cart Item
```swift
@Model
final class PersistedCartItem {
    @Attribute(.unique) var sku: String
    var qty: Int
    var cart: PersistedCart?
    
    init(sku: String, qty: Int) {
        self.sku = sku
        self.qty = qty
    }
}
```

### Persisted Cart
```swift
@Model
final class PersistedCart {
    @Attribute(.unique) var ownerId: String
    var version: Int
    @Relationship(deleteRule: .cascade, inverse: \PersistedCartItem.cart)
    var items: [PersistedCartItem]
    
    init(ownerId: String, version: Int = 0) {
        self.ownerId = ownerId
        self.version = version
        self.items = []
    }
}
```

### Persisted Outbox Operation
```swift
@Model
final class PersistedCartOp {
    @Attribute(.unique) var opId: UUID  // Survives app restart
    var ownerIdData: Data               // Encoded CartOwner
    var typeData: Data                  // Encoded CartOpType
    var attempts: Int
    var nextRetryAt: Date
    
    init(op: CartOp) throws {
        self.opId = op.id
        self.ownerIdData = try JSONEncoder().encode(op.owner)
        self.typeData = try JSONEncoder().encode(op.type)
        self.attempts = op.attempts
        self.nextRetryAt = op.nextRetryAt
    }
}
```

## Model Container Setup

```swift
@main
struct App: App {
    let container: ModelContainer
    
    init() {
        let schema = Schema([
            PersistedCart.self,
            PersistedCartItem.self,
            PersistedCartOp.self
        ])
        
        let configuration = ModelConfiguration(
            "ECommerceStore",
            schema: schema,
            isStoredInMemoryOnly: false
        )
        
        container = try! ModelContainer(
            for: schema,
            configurations: [configuration]
        )
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(container)
    }
}
```

## Persistent Store Implementation

```swift
public actor PersistentCartStore: CartStoreProtocol {
    private let container: ModelContainer
    
    public init(container: ModelContainer) {
        self.container = container
    }
    
    public func get(owner: CartOwner) async -> Cart {
        let context = ModelContext(container)
        let ownerId = owner.id
        
        let descriptor = FetchDescriptor<PersistedCart>(
            predicate: #Predicate { $0.ownerId == ownerId }
        )
        
        guard let persisted = try? context.fetch(descriptor).first else {
            return Cart.empty(owner: owner)
        }
        
        return Cart(
            owner: owner,
            items: persisted.items.map { CartItem(sku: $0.sku, qty: $0.qty) },
            version: persisted.version
        )
    }
    
    public func upsert(_ cart: Cart) async {
        let context = ModelContext(container)
        let ownerId = cart.owner.id
        
        // Delete existing
        try? context.delete(model: PersistedCart.self, where: #Predicate {
            $0.ownerId == ownerId
        })
        
        // Insert new
        let persisted = PersistedCart(ownerId: ownerId, version: cart.version)
        persisted.items = cart.items.map {
            PersistedCartItem(sku: $0.sku, qty: $0.qty)
        }
        context.insert(persisted)
        
        try? context.save()
    }
}
```

## Swappable Architecture

```swift
// In-memory for development/testing
let cartStore = CartStore()

// SwiftData for production
let cartStore = PersistentCartStore(container: .shared)

// Both conform to CartStoreProtocol - Repository doesn't know the difference
let repository = CartRepository(store: cartStore, ...)
```

## Key Patterns

| Pattern | Implementation |
|---------|----------------|
| Protocol abstraction | `CartStoreProtocol` |
| Actor isolation | `@ModelActor` or manual context |
| Unique constraints | `@Attribute(.unique)` |
| Cascade delete | `@Relationship(deleteRule: .cascade)` |
| Swappable stores | Protocol + DI |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devzahirul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
