---
name: solid-principles
description: Five principles for maintainable object-oriented design Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: SOLID Principles

Design software that is easy to maintain, extend, and understand.

## Single Responsibility Principle (SRP)

A class/module should have only one reason to change.

### Rules

- ✅ DO: Keep classes focused on one responsibility
- ✅ DO: If describing a class requires "and", it does too much
- ✅ DO: Extract separate concerns into separate modules
- ❌ DON'T: Create god classes that do everything

### Examples

```typescript
// ❌ Bad - multiple responsibilities
class UserManager {
  createUser(data: UserData) {
    /* ... */
  }
  validateEmail(email: string) {
    /* ... */
  }
  sendWelcomeEmail(user: User) {
    /* ... */
  }
  generateReport(users: User[]) {
    /* ... */
  }
  exportToCSV(users: User[]) {
    /* ... */
  }
}

// ✅ Good - separated responsibilities
class UserService {
  createUser(data: UserData) {
    /* ... */
  }
}

class EmailValidator {
  validate(email: string) {
    /* ... */
  }
}

class EmailService {
  sendWelcome(user: User) {
    /* ... */
  }
}

class UserReportGenerator {
  generate(users: User[]) {
    /* ... */
  }
  exportToCSV(users: User[]) {
    /* ... */
  }
}
```

## Open/Closed Principle (OCP)

Open for extension, closed for modification.

### Rules

- ✅ DO: Use abstractions to allow extension
- ✅ DO: Use strategy pattern, plugins, or dependency injection
- ✅ DO: Add new behavior by adding code, not changing existing
- ❌ DON'T: Modify existing code to add new features

### Examples

```typescript
// ❌ Bad - must modify to add new payment types
class PaymentProcessor {
  process(payment: Payment) {
    if (payment.type === "credit") {
      // process credit
    } else if (payment.type === "debit") {
      // process debit
    } else if (payment.type === "crypto") {
      // had to modify existing code!
    }
  }
}

// ✅ Good - extend without modifying
interface PaymentStrategy {
  process(payment: Payment): Promise<Result>;
}

class CreditPayment implements PaymentStrategy {
  process(payment: Payment) {
    /* ... */
  }
}

class CryptoPayment implements PaymentStrategy {
  process(payment: Payment) {
    /* ... */
  }
}

class PaymentProcessor {
  constructor(private strategies: Map<string, PaymentStrategy>) {}

  process(payment: Payment) {
    const strategy = this.strategies.get(payment.type);
    return strategy.process(payment);
  }
}
```

## Liskov Substitution Principle (LSP)

Subtypes must be substitutable for their base types.

### Rules

- ✅ DO: Ensure derived classes can replace base classes
- ✅ DO: Maintain expected behavior in subclasses
- ✅ DO: Favor composition over inheritance when LSP is hard
- ❌ DON'T: Override methods to throw "not implemented"
- ❌ DON'T: Violate parent class contracts

### Examples

```typescript
// ❌ Bad - violates LSP
class Rectangle {
  constructor(
    public width: number,
    public height: number,
  ) {}

  setWidth(w: number) {
    this.width = w;
  }
  setHeight(h: number) {
    this.height = h;
  }
  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(w: number) {
    this.width = w;
    this.height = w; // Unexpected side effect!
  }
}

// ✅ Good - use composition or separate types
interface Shape {
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(
    public width: number,
    public height: number,
  ) {}
  getArea() {
    return this.width * this.height;
  }
}

class Square implements Shape {
  constructor(public side: number) {}
  getArea() {
    return this.side * this.side;
  }
}
```

## Interface Segregation Principle (ISP)

Many specific interfaces are better than one general interface.

### Rules

- ✅ DO: Create focused, specific interfaces
- ✅ DO: Split large interfaces into smaller ones
- ✅ DO: Clients should only depend on methods they use
- ❌ DON'T: Force implementations to have unused methods

### Examples

```typescript
// ❌ Bad - fat interface
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
  attendMeeting(): void;
}

// Robot can't eat or sleep!
class Robot implements Worker {
  work() {
    /* ... */
  }
  eat() {
    throw new Error("Not implemented");
  }
  sleep() {
    throw new Error("Not implemented");
  }
  attendMeeting() {
    throw new Error("Not implemented");
  }
}

// ✅ Good - segregated interfaces
interface Workable {
  work(): void;
}

interface Feedable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class Human implements Workable, Feedable, Sleepable {
  work() {
    /* ... */
  }
  eat() {
    /* ... */
  }
  sleep() {
    /* ... */
  }
}

class Robot implements Workable {
  work() {
    /* ... */
  }
}
```

## Dependency Inversion Principle (DIP)

Depend on abstractions, not concretions.

### Rules

- ✅ DO: High-level modules should not depend on low-level modules
- ✅ DO: Both should depend on abstractions
- ✅ DO: Inject dependencies instead of creating them
- ❌ DON'T: Instantiate dependencies directly inside classes

### Examples

```typescript
// ❌ Bad - depends on concretion
class UserService {
  private database = new MySQLDatabase(); // Hard dependency

  getUser(id: string) {
    return this.database.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}

// ✅ Good - depends on abstraction
interface Database {
  query<T>(sql: string): Promise<T>;
}

class UserService {
  constructor(private database: Database) {} // Injected

  getUser(id: string) {
    return this.database.query(`SELECT * FROM users WHERE id = ?`, [id]);
  }
}

// Easy to test with mock, easy to swap implementations
const userService = new UserService(new MySQLDatabase());
const testService = new UserService(new MockDatabase());
```

## Summary

| Principle | Remember                   |
| --------- | -------------------------- |
| **S**RP   | One reason to change       |
| **O**CP   | Extend, don't modify       |
| **L**SP   | Subtypes are substitutable |
| **I**SP   | Small, focused interfaces  |
| **D**IP   | Depend on abstractions     |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
