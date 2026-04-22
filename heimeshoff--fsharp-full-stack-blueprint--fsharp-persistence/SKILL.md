---
name: fsharp-persistence
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Persistence Patterns

## Philosophy: I/O at the Edges

Persistence is where data meets the outside world. Keep it isolated, explicit about effects, and separate from business logic. The rest of your code should be pure; persistence is where impurity lives.

**Before implementing persistence, ask:**
- What's the simplest storage that meets the requirements?
- What queries do I need? (Design for access patterns)
- What needs to be transactional?
- How do I handle "not found" vs errors?

**Core Principles:**

1. **Async Everything**: All I/O is async. No blocking database calls in an async application.

2. **Options for Absence**: When something might not exist, return `Option<'T>`. Don't return null, don't throw exceptions.

3. **Parameterized Always**: Every query uses parameters. SQL injection is never acceptable.

4. **Dispose Connections**: Use `use` to ensure connections are closed. Leaking connections kills applications.

---

## Storage Choices

| Storage | Use When |
|---------|----------|
| **SQLite** | Relational data, queries, production persistence |
| **JSON Files** | Simple data, prototyping, configuration |
| **Event Sourcing** | Audit trails, temporal queries, complex domains |

Choose the simplest option that meets your needs.

---

## SQLite with Dapper

### Setup

```fsharp
module Persistence

open System
open System.Data
open Microsoft.Data.Sqlite
open Dapper

// Environment-based configuration
let private connectionString =
    match Environment.GetEnvironmentVariable("DATABASE_PATH") with
    | null | "" -> "Data Source=./data/app.db;Mode=ReadWriteCreate"
    | path -> $"Data Source={path};Mode=ReadWriteCreate"

let getConnection () : SqliteConnection =
    new SqliteConnection(connectionString)

let ensureDataDir () =
    let dir = "./data"
    if not (IO.Directory.Exists dir) then
        IO.Directory.CreateDirectory dir |> ignore
```

### Initialize Schema

```fsharp
let initializeDatabase () =
    ensureDataDir()
    use conn = getConnection()
    conn.Open()

    // Create tables
    conn.Execute("""
        CREATE TABLE IF NOT EXISTS Orders (
            Id INTEGER PRIMARY KEY AUTOINCREMENT,
            CustomerId INTEGER NOT NULL,
            Status TEXT NOT NULL,
            Total REAL NOT NULL,
            CreatedAt TEXT NOT NULL,
            UpdatedAt TEXT NOT NULL
        )
    """) |> ignore

    conn.Execute("""
        CREATE TABLE IF NOT EXISTS OrderItems (
            Id INTEGER PRIMARY KEY AUTOINCREMENT,
            OrderId INTEGER NOT NULL,
            ProductId INTEGER NOT NULL,
            ProductName TEXT NOT NULL,
            Quantity INTEGER NOT NULL,
            UnitPrice REAL NOT NULL,
            FOREIGN KEY (OrderId) REFERENCES Orders(Id)
        )
    """) |> ignore

    // Create indexes for common queries
    conn.Execute("""
        CREATE INDEX IF NOT EXISTS idx_orders_customer
        ON Orders(CustomerId)
    """) |> ignore

    conn.Execute("""
        CREATE INDEX IF NOT EXISTS idx_orders_status
        ON Orders(Status)
    """) |> ignore
```

### CRUD Operations

**Read All**
```fsharp
let getAllOrders () : Async<Order list> =
    async {
        use conn = getConnection()
        let! orders = conn.QueryAsync<Order>(
            "SELECT * FROM Orders ORDER BY CreatedAt DESC"
        ) |> Async.AwaitTask
        return orders |> Seq.toList
    }
```

**Read with Filter**
```fsharp
let getOrdersByCustomer (customerId: int) : Async<Order list> =
    async {
        use conn = getConnection()
        let! orders = conn.QueryAsync<Order>(
            "SELECT * FROM Orders WHERE CustomerId = @CustomerId ORDER BY CreatedAt DESC",
            {| CustomerId = customerId |}
        ) |> Async.AwaitTask
        return orders |> Seq.toList
    }

let getOrdersByStatus (status: OrderStatus) : Async<Order list> =
    async {
        use conn = getConnection()
        let! orders = conn.QueryAsync<Order>(
            "SELECT * FROM Orders WHERE Status = @Status",
            {| Status = string status |}
        ) |> Async.AwaitTask
        return orders |> Seq.toList
    }
```

**Read Single (may not exist)**
```fsharp
let getOrderById (id: int) : Async<Order option> =
    async {
        use conn = getConnection()
        let! order = conn.QuerySingleOrDefaultAsync<Order>(
            "SELECT * FROM Orders WHERE Id = @Id",
            {| Id = id |}
        ) |> Async.AwaitTask
        return if isNull (box order) then None else Some order
    }
```

**Insert**
```fsharp
let insertOrder (order: Order) : Async<Order> =
    async {
        use conn = getConnection()
        let! id = conn.ExecuteScalarAsync<int64>(
            """INSERT INTO Orders (CustomerId, Status, Total, CreatedAt, UpdatedAt)
               VALUES (@CustomerId, @Status, @Total, @CreatedAt, @UpdatedAt)
               RETURNING Id""",
            {|
                CustomerId = order.CustomerId
                Status = string order.Status
                Total = order.Total
                CreatedAt = order.CreatedAt.ToString("o")
                UpdatedAt = order.UpdatedAt.ToString("o")
            |}
        ) |> Async.AwaitTask
        return { order with Id = int id }
    }
```

**Update**
```fsharp
let updateOrder (order: Order) : Async<unit> =
    async {
        use conn = getConnection()
        let! _ = conn.ExecuteAsync(
            """UPDATE Orders
               SET CustomerId = @CustomerId,
                   Status = @Status,
                   Total = @Total,
                   UpdatedAt = @UpdatedAt
               WHERE Id = @Id""",
            {|
                Id = order.Id
                CustomerId = order.CustomerId
                Status = string order.Status
                Total = order.Total
                UpdatedAt = DateTime.UtcNow.ToString("o")
            |}
        ) |> Async.AwaitTask
        return ()
    }
```

**Delete**
```fsharp
let deleteOrder (id: int) : Async<unit> =
    async {
        use conn = getConnection()
        let! _ = conn.ExecuteAsync(
            "DELETE FROM Orders WHERE Id = @Id",
            {| Id = id |}
        ) |> Async.AwaitTask
        return ()
    }
```

### Transactions

For operations that must succeed or fail together:

```fsharp
let createOrderWithItems (order: Order) (items: OrderItem list) : Async<Order> =
    async {
        use conn = getConnection()
        conn.Open()
        use transaction = conn.BeginTransaction()

        try
            // Insert order
            let! orderId = conn.ExecuteScalarAsync<int64>(
                """INSERT INTO Orders (CustomerId, Status, Total, CreatedAt, UpdatedAt)
                   VALUES (@CustomerId, @Status, @Total, @CreatedAt, @UpdatedAt)
                   RETURNING Id""",
                {| CustomerId = order.CustomerId; Status = string order.Status;
                   Total = order.Total; CreatedAt = order.CreatedAt.ToString("o");
                   UpdatedAt = order.UpdatedAt.ToString("o") |},
                transaction
            ) |> Async.AwaitTask

            // Insert items
            for item in items do
                let! _ = conn.ExecuteAsync(
                    """INSERT INTO OrderItems (OrderId, ProductId, ProductName, Quantity, UnitPrice)
                       VALUES (@OrderId, @ProductId, @ProductName, @Quantity, @UnitPrice)""",
                    {| OrderId = int orderId; ProductId = item.ProductId;
                       ProductName = item.ProductName; Quantity = item.Quantity;
                       UnitPrice = item.UnitPrice |},
                    transaction
                ) |> Async.AwaitTask
                ()

            transaction.Commit()
            return { order with Id = int orderId }
        with ex ->
            transaction.Rollback()
            return raise ex
    }
```

---

## JSON File Storage

For simple storage without a database:

```fsharp
open System.IO
open System.Text.Json
open System.Threading

let private dataDir = "./data"
let private dataFile = Path.Combine(dataDir, "orders.json")
let private fileLock = new SemaphoreSlim(1, 1)

let ensureDataDir () =
    if not (Directory.Exists dataDir) then
        Directory.CreateDirectory dataDir |> ignore

let loadOrders () : Async<Order list> =
    async {
        do! fileLock.WaitAsync() |> Async.AwaitTask
        try
            ensureDataDir()
            if File.Exists dataFile then
                let! json = File.ReadAllTextAsync dataFile |> Async.AwaitTask
                let options = JsonSerializerOptions(PropertyNameCaseInsensitive = true)
                return JsonSerializer.Deserialize<Order list>(json, options)
            else
                return []
        finally
            fileLock.Release() |> ignore
    }

let saveOrders (orders: Order list) : Async<unit> =
    async {
        do! fileLock.WaitAsync() |> Async.AwaitTask
        try
            ensureDataDir()
            let options = JsonSerializerOptions(WriteIndented = true)
            let json = JsonSerializer.Serialize(orders, options)
            do! File.WriteAllTextAsync(dataFile, json) |> Async.AwaitTask
        finally
            fileLock.Release() |> ignore
    }

// CRUD using load/save
let getAllOrders () = loadOrders()

let getOrderById (id: int) : Async<Order option> =
    async {
        let! orders = loadOrders()
        return orders |> List.tryFind (fun o -> o.Id = id)
    }

let insertOrder (order: Order) : Async<Order> =
    async {
        let! orders = loadOrders()
        let newId = if orders.IsEmpty then 1 else (orders |> List.map (fun o -> o.Id) |> List.max) + 1
        let newOrder = { order with Id = newId }
        do! saveOrders (newOrder :: orders)
        return newOrder
    }

let updateOrder (order: Order) : Async<unit> =
    async {
        let! orders = loadOrders()
        let updated = orders |> List.map (fun o -> if o.Id = order.Id then order else o)
        do! saveOrders updated
    }

let deleteOrder (id: int) : Async<unit> =
    async {
        let! orders = loadOrders()
        let remaining = orders |> List.filter (fun o -> o.Id <> id)
        do! saveOrders remaining
    }
```

**When to use JSON files:**
- Prototyping before committing to a database
- Simple applications with low write volume
- Configuration or settings storage

**When NOT to use:**
- High write concurrency
- Large datasets
- Complex queries

---

## Event Sourcing

For when you need audit trails or temporal queries:

```fsharp
type OrderEvent =
    | OrderCreated of Order
    | OrderUpdated of Order
    | OrderStatusChanged of orderId: int * status: OrderStatus
    | OrderDeleted of orderId: int

type EventEnvelope = {
    Id: Guid
    Timestamp: DateTime
    Event: OrderEvent
}

let private eventFile = Path.Combine(dataDir, "events.jsonl")

let appendEvent (event: OrderEvent) : Async<unit> =
    async {
        do! fileLock.WaitAsync() |> Async.AwaitTask
        try
            ensureDataDir()
            let envelope = {
                Id = Guid.NewGuid()
                Timestamp = DateTime.UtcNow
                Event = event
            }
            let json = JsonSerializer.Serialize(envelope)
            do! File.AppendAllTextAsync(eventFile, json + "\n") |> Async.AwaitTask
        finally
            fileLock.Release() |> ignore
    }

let readAllEvents () : Async<EventEnvelope list> =
    async {
        if File.Exists eventFile then
            let! lines = File.ReadAllLinesAsync eventFile |> Async.AwaitTask
            return
                lines
                |> Array.filter (not << String.IsNullOrWhiteSpace)
                |> Array.map (fun line -> JsonSerializer.Deserialize<EventEnvelope>(line))
                |> Array.toList
        else
            return []
    }

// Rebuild current state from events
let rebuildState () : Async<Map<int, Order>> =
    async {
        let! events = readAllEvents()

        let applyEvent (state: Map<int, Order>) (envelope: EventEnvelope) =
            match envelope.Event with
            | OrderCreated order -> Map.add order.Id order state
            | OrderUpdated order -> Map.add order.Id order state
            | OrderStatusChanged (id, status) ->
                state
                |> Map.tryFind id
                |> Option.map (fun o -> Map.add id { o with Status = status } state)
                |> Option.defaultValue state
            | OrderDeleted id -> Map.remove id state

        return events |> List.fold applyEvent Map.empty
    }
```

**When to use event sourcing:**
- Audit requirements (who did what when)
- Complex domains with undo/redo
- Event-driven architectures
- Temporal queries (state at any point in time)

---

## Anti-Patterns to Avoid

❌ **String Concatenation for Queries**
```fsharp
// BAD: SQL injection vulnerability
let query = $"SELECT * FROM Users WHERE Name = '{name}'"
```
*Why bad*: Security vulnerability.
*Better*: Always use parameterized queries.

❌ **Forgetting to Dispose**
```fsharp
// BAD: Connection leak
let getOrders () =
    let conn = getConnection()  // Never disposed!
    conn.Query<Order>("SELECT * FROM Orders")
```
*Why bad*: Connections leak, application eventually fails.
*Better*: Use `use conn = getConnection()`.

❌ **Sync I/O in Async Context**
```fsharp
// BAD: Blocking call
let getOrders () = async {
    use conn = getConnection()
    return conn.Query<Order>(...)  // Blocking!
}
```
*Why bad*: Blocks thread pool, reduces throughput.
*Better*: Use `QueryAsync` with `Async.AwaitTask`.

❌ **Throwing on Not Found**
```fsharp
// BAD
let getOrder id =
    match findOrder id with
    | null -> failwith "Order not found"
    | order -> order
```
*Why bad*: Exception for expected case.
*Better*: Return `Option<Order>`.

---

## Variation Guidance

**Small application**: JSON file storage, migrate to SQLite when needed.

**Standard CRUD**: SQLite with Dapper, simple queries.

**Complex queries**: Consider full SQL with joins, CTEs.

**Audit requirements**: Event sourcing with periodic snapshots.

**High performance**: Connection pooling, batch operations, caching.

Match the persistence strategy to your actual requirements.

---

## Remember

Persistence is the boundary between your pure code and the messy outside world. Keep it clean, keep it isolated, and handle failures gracefully. Your domain logic shouldn't care whether data lives in SQLite, files, or a REST API.

**The goal**: Persistence code that's boring—reliable, predictable, and invisible to the rest of the application.

## Related Documentation

- `/docs/05-PERSISTENCE.md` - Detailed persistence patterns
- `/docs/03-BACKEND-GUIDE.md` - Backend architecture

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
