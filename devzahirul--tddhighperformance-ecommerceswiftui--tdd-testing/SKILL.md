---
name: tdd-testing
description: Test-Driven Development patterns with unit tests, mocks, and async testing Use when this capability is needed.
metadata:
  author: devzahirul
---

# TDD Testing Skill

## Overview
Test-Driven Development practices for iOS including unit testing, mock creation, and testing async code.

## Test Structure

```
ScalableHighQualityECommerce/
├── Tests/
│   ├── CartRepositoryTests.swift
│   └── MockHTTPClient.swift
│
└── ScalableHighQualityECommerceUITests/
    └── ScalableHighQualityECommerceUITests.swift
```

## Unit Test Pattern (Given-When-Then)

```swift
func test_addItem_updatesLocalCartOptimistically() async {
    // Given
    let owner = CartOwner.guest("test-anon-123")
    let store = CartStore()
    let outbox = OutboxStore()
    let sut = CartRepository(store: store, outbox: outbox)
    
    // When
    await sut.addItem(owner: owner, sku: "SKU-TEST", qty: 2)
    
    // Then
    let cart = await sut.getCart(owner: owner)
    XCTAssertEqual(cart.items.first?.qty, 2)
}
```

## Mock HTTP Client

```swift
public actor MockHTTPClient: HTTPClient {
    public private(set) var recordedRequests: [HTTPRequest] = []
    private var stubs: [String: Result<HTTPResponse, Error>] = [:]
    
    public func stub(path: String, response: HTTPResponse) {
        stubs[path] = .success(response)
    }
    
    public func stubError(path: String, error: Error) {
        stubs[path] = .failure(error)
    }
    
    public func execute(_ request: HTTPRequest) async throws -> HTTPResponse {
        recordedRequests.append(request)
        
        guard let result = stubs[request.path] else {
            throw MockError.noStub
        }
        
        switch result {
        case .success(let response): return response
        case .failure(let error): throw error
        }
    }
    
    public func wasRequestMade(to path: String) -> Bool {
        recordedRequests.contains { $0.path == path }
    }
}
```

## Testing Async Code

### Waiting for Actor Operations

```swift
func test_outboxEnqueuesOperation() async {
    // Given
    let outbox = OutboxStore()
    let sut = CartRepository(store: CartStore(), outbox: outbox)
    
    // When
    await sut.addItem(owner: .guest("test"), sku: "SKU-1", qty: 1)
    
    // Then
    let pending = await outbox.pending()
    XCTAssertEqual(pending.count, 1)
}
```

### Testing AsyncStream

```swift
func test_streamEmitsUpdates() async {
    // Given
    let store = CartStore()
    var received: [Cart] = []
    
    // When
    let task = Task {
        for await cart in store.stream(owner: .guest("test")) {
            received.append(cart)
            if received.count >= 2 { break }
        }
    }
    
    await store.upsert(Cart(owner: .guest("test"), items: [item]))
    await task.value
    
    // Then
    XCTAssertEqual(received.count, 2)
}
```

## Mock Store Pattern

```swift
actor MockCartStore: CartStoreProtocol {
    private var carts: [CartOwner: Cart] = [:]
    private(set) var upsertCallCount = 0
    
    func get(owner: CartOwner) -> Cart {
        carts[owner] ?? Cart.empty(owner: owner)
    }
    
    func upsert(_ cart: Cart) {
        carts[cart.owner] = cart
        upsertCallCount += 1
    }
}
```

## Test Commands

```bash
# Run all tests
xcodebuild test -scheme ScalableHighQualityECommerce \
    -destination 'platform=iOS Simulator,name=iPhone 15'

# Run with coverage
xcodebuild test -scheme ScalableHighQualityECommerce \
    -enableCodeCoverage YES

# Run specific test
xcodebuild test -scheme ScalableHighQualityECommerce \
    -only-testing:ScalableHighQualityECommerceTests/CartRepositoryTests
```

## Best Practices

| Practice | Description |
|----------|-------------|
| Test behavior, not implementation | Assert on outcomes |
| One assertion per test | Easier to debug |
| Descriptive test names | `test_action_expectedResult` |
| Use protocol mocks | Not concrete implementations |
| Fast feedback | Mock all external dependencies |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devzahirul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
