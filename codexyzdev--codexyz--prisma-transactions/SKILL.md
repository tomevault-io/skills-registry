---
name: prisma-transactions
description: Comprehensive guide and reference for using Prisma Transactions, including sequential operations, interactive transactions, nested writes, and optimistic concurrency control. Use when the user asks about handling transactions, atomicity, batch operations, or preventing race conditions in Prisma. Use when this capability is needed.
metadata:
  author: codexyzdev
---

# Prisma Transactions Skill

This skill provides detailed information and examples for using transactions in Prisma.

## Available Resources

- **Overview**: [overview.md](references/overview.md) - General concepts, ACID properties, and available techniques.
- **Nested Writes**: [nested-writes.md](references/nested-writes.md) - Creating/Updating related records atomically (dependent writes).
- **Batch Operations**: [batch-operations.md](references/batch-operations.md) - Bulk operations like `createMany`, `updateMany`, `deleteMany`.
- **Transaction API**: [transaction-api.md](references/transaction-api.md) - Sequential (`$transaction([])`) and Interactive (`$transaction(fn)`) transactions.
- **Concurrency Patterns**: [concurrency-patterns.md](references/concurrency-patterns.md) - Idempotency and Optimistic Concurrency Control (OCC).

## Common Use Cases

1.  **Sequential Operations**: Executing a list of queries in order. See [transaction-api.md](references/transaction-api.md).
2.  **Interactive Transactions**: Using a function to run logic within a transaction. See [transaction-api.md](references/transaction-api.md).
3.  **Nested Writes**: Creating/Updating related records atomically. See [nested-writes.md](references/nested-writes.md).
4.  **Optimistic Concurrency Control**: Handling race conditions without locks. See [concurrency-patterns.md](references/concurrency-patterns.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codexyzdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
