---
name: database-design
description: > Use when this capability is needed.
metadata:
  author: bramato
---

# Database Design with Laravel

## Migration Best Practices

### Naming Conventions

Laravel uses snake_case naming for migration files. The artisan generator infers the
table name from the migration name:

```bash
# Creating tables
php artisan make:migration create_orders_table
php artisan make:migration create_order_items_table

# Modifying tables
php artisan make:migration add_status_to_orders_table
php artisan make:migration add_tracking_number_to_orders_table
php artisan make:migration rename_title_to_name_on_products_table
php artisan make:migration drop_legacy_column_from_users_table
```

### One Change Per Migration

Each migration should do one logical thing. This makes rollbacks predictable:

```php
// Good: single responsibility
// create_orders_table migration
public function up(): void
{
    Schema::create('orders', function (Blueprint $table) {
        $table->id();
        $table->foreignId('user_id')->constrained()->cascadeOnDelete();
        $table->foreignId('customer_id')->constrained();
        $table->string('order_number')->unique();
        $table->enum('status', ['pending', 'confirmed', 'shipped', 'delivered', 'cancelled'])
            ->default('pending');
        $table->decimal('subtotal', 10, 2);
        $table->decimal('tax', 10, 2)->default(0);
        $table->decimal('total', 10, 2);
        $table->text('notes')->nullable();
        $table->timestamp('shipped_at')->nullable();
        $table->timestamp('delivered_at')->nullable();
        $table->timestamps();
        $table->softDeletes();
    });
}

public function down(): void
{
    Schema::dropIfExists('orders');
}
```

### Always Implement down()

The `down()` method must reverse exactly what `up()` does. This allows safe rollbacks:

```php
// add_status_to_orders_table
public function up(): void
{
    Schema::table('orders', function (Blueprint $table) {
        $table->string('status')->default('pending')->after('total');
    });
}

public function down(): void
{
    Schema::table('orders', function (Blueprint $table) {
        $table->dropColumn('status');
    });
}
```

### Standard Column Helpers

Always use the built-in helpers for consistency:

```php
$table->id();                    // BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
$table->uuid('id')->primary();  // UUID primary key alternative
$table->ulid('id')->primary();  // ULID primary key alternative
$table->timestamps();            // created_at and updated_at TIMESTAMP NULLABLE
$table->softDeletes();           // deleted_at TIMESTAMP NULLABLE
$table->rememberToken();         // remember_token VARCHAR(100) NULLABLE
```

---

## Column Types Reference

| Migration Method               | MySQL Type              | PostgreSQL Type     | Purpose                          |
|---------------------------------|-------------------------|---------------------|----------------------------------|
| `$table->id()`                  | BIGINT UNSIGNED AI PK   | BIGSERIAL PK        | Auto-incrementing primary key    |
| `$table->uuid('id')`           | CHAR(36)                | UUID                | UUID column                      |
| `$table->ulid('id')`           | CHAR(26)                | CHAR(26)            | ULID column                      |
| `$table->string('name')`       | VARCHAR(255)            | VARCHAR(255)        | Short text (names, emails)       |
| `$table->string('code', 10)`   | VARCHAR(10)             | VARCHAR(10)         | Short text with max length       |
| `$table->text('body')`         | TEXT                    | TEXT                | Long text (descriptions)         |
| `$table->mediumText('content')`| MEDIUMTEXT              | TEXT                | Medium-length content            |
| `$table->longText('payload')`  | LONGTEXT                | TEXT                | Very long content (JSON blobs)   |
| `$table->integer('qty')`       | INT                     | INTEGER             | Standard integer                 |
| `$table->bigInteger('views')`  | BIGINT                  | BIGINT              | Large integer                    |
| `$table->unsignedInteger('qty')`| INT UNSIGNED           | INTEGER             | Non-negative integer             |
| `$table->tinyInteger('level')` | TINYINT                 | SMALLINT            | Small integer (0-127)            |
| `$table->boolean('active')`    | TINYINT(1)              | BOOLEAN             | True/false flag                  |
| `$table->decimal('price', 10, 2)`| DECIMAL(10,2)         | NUMERIC(10,2)       | Exact decimal (money)            |
| `$table->float('rating')`      | FLOAT                   | REAL                | Approximate decimal              |
| `$table->date('birth_date')`   | DATE                    | DATE                | Date without time                |
| `$table->dateTime('starts_at')`| DATETIME                | TIMESTAMP           | Date with time                   |
| `$table->timestamp('sent_at')` | TIMESTAMP               | TIMESTAMP           | Timestamp                        |
| `$table->time('opens_at')`     | TIME                    | TIME                | Time without date                |
| `$table->year('vintage')`      | YEAR                    | INTEGER             | Year only                        |
| `$table->json('metadata')`     | JSON                    | JSONB               | JSON data                        |
| `$table->enum('status', [...])`| ENUM                    | VARCHAR + CHECK     | Enumerated values                |
| `$table->binary('photo')`      | BLOB                    | BYTEA               | Binary data                      |
| `$table->ipAddress('visitor')` | VARCHAR(45)             | INET                | IPv4 or IPv6 address             |
| `$table->macAddress('device')` | VARCHAR(17)             | MACADDR             | MAC address                      |

### Column Modifiers

```php
$table->string('email')->unique();            // Unique constraint
$table->string('nickname')->nullable();       // Allow NULL
$table->string('status')->default('active');  // Default value
$table->integer('position')->unsigned();      // Unsigned (non-negative)
$table->text('bio')->nullable()->after('email'); // Column ordering (MySQL)
$table->string('slug')->comment('URL slug');  // Column comment
$table->string('legacy')->virtualAs("CONCAT(first_name, ' ', last_name)"); // Generated column
```

---

## Index Strategies

### When to Add Indexes

Add indexes on columns that appear in:
- `WHERE` clauses (equality and range lookups)
- `JOIN` conditions (foreign keys)
- `ORDER BY` clauses (sorting)
- `GROUP BY` clauses (aggregation)

Do NOT index columns that:
- Have very low cardinality (e.g., a boolean with 50/50 distribution on a small table)
- Are rarely queried
- Are on tables with very few rows

### Creating Indexes

```php
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained();
    $table->string('status');
    $table->timestamp('created_at');

    // Single column index
    $table->index('status');

    // Composite index (column order matters — leftmost prefix rule)
    $table->index(['user_id', 'status', 'created_at']);

    // Unique constraint (also creates an index)
    $table->unique('order_number');

    // Composite unique
    $table->unique(['user_id', 'product_id']);
});
```

### Composite Index Column Order

The leftmost prefix rule means a composite index `(user_id, status, created_at)` can
satisfy queries that filter on:
- `user_id` alone
- `user_id` AND `status`
- `user_id` AND `status` AND `created_at`

It will NOT help queries that filter on:
- `status` alone (needs its own index)
- `created_at` alone

Put the most selective column first, and the range column last.

### Full-Text Indexes

```php
$table->fullText('body');                      // Single column
$table->fullText(['title', 'body']);            // Multi-column

// Querying
Product::whereFullText('description', 'wireless headphones')->get();
Product::whereFullText(['title', 'description'], 'wireless headphones')->get();
```

### Adding Indexes to Existing Tables

```php
// add_index_to_orders_table migration
public function up(): void
{
    Schema::table('orders', function (Blueprint $table) {
        $table->index('status', 'orders_status_index');
        $table->index(['user_id', 'created_at'], 'orders_user_created_index');
    });
}

public function down(): void
{
    Schema::table('orders', function (Blueprint $table) {
        $table->dropIndex('orders_status_index');
        $table->dropIndex('orders_user_created_index');
    });
}
```

---

## Foreign Key Conventions

### Standard Foreign Keys

```php
// Shorthand (Laravel convention: column name infers table)
$table->foreignId('user_id')->constrained()->cascadeOnDelete();

// Explicit table reference
$table->foreignId('author_id')->constrained('users')->cascadeOnDelete();

// Nullable foreign key
$table->foreignId('category_id')->nullable()->constrained()->nullOnDelete();
```

### On Delete Behaviors

| Method                | SQL                  | Behavior                                |
|-----------------------|----------------------|-----------------------------------------|
| `cascadeOnDelete()`   | ON DELETE CASCADE    | Delete child rows when parent is deleted|
| `nullOnDelete()`      | ON DELETE SET NULL   | Set foreign key to NULL (column must be nullable) |
| `restrictOnDelete()`  | ON DELETE RESTRICT   | Prevent parent deletion if children exist |
| `noActionOnDelete()`  | ON DELETE NO ACTION  | Same as RESTRICT in most databases      |

### On Update Behaviors

```php
$table->foreignId('user_id')->constrained()->cascadeOnUpdate()->cascadeOnDelete();
```

### Choosing the Right Delete Behavior

- **cascadeOnDelete**: Order -> OrderItems (items are meaningless without the order)
- **nullOnDelete**: Post -> author_id (keep the post even if the user is deleted)
- **restrictOnDelete**: User -> Orders (prevent deleting users who have orders)

---

## Eloquent Relationships

### 1. One-to-One: hasOne / belongsTo

```php
// Migration: create_profiles_table
$table->id();
$table->foreignId('user_id')->unique()->constrained()->cascadeOnDelete();
$table->string('bio')->nullable();
$table->string('avatar_url')->nullable();
$table->timestamps();

// User model
public function profile(): HasOne
{
    return $this->hasOne(Profile::class);
}

// Profile model
public function user(): BelongsTo
{
    return $this->belongsTo(User::class);
}

// Usage
$user->profile;
$user->profile()->create(['bio' => 'Developer']);
$profile->user;
```

### 2. One-to-Many: hasMany / belongsTo

```php
// Migration: create_order_items_table
$table->id();
$table->foreignId('order_id')->constrained()->cascadeOnDelete();
$table->foreignId('product_id')->constrained();
$table->integer('quantity');
$table->decimal('unit_price', 10, 2);
$table->timestamps();

// Order model
public function items(): HasMany
{
    return $this->hasMany(OrderItem::class);
}

// OrderItem model
public function order(): BelongsTo
{
    return $this->belongsTo(Order::class);
}

// Usage
$order->items;
$order->items()->where('quantity', '>', 1)->get();
$order->items()->create([...]);
```

### 3. Many-to-Many: belongsToMany

```php
// Migration: create_product_tag_table (pivot)
$table->id();
$table->foreignId('product_id')->constrained()->cascadeOnDelete();
$table->foreignId('tag_id')->constrained()->cascadeOnDelete();
$table->timestamps();
$table->unique(['product_id', 'tag_id']);

// Product model
public function tags(): BelongsToMany
{
    return $this->belongsToMany(Tag::class)->withTimestamps();
}

// Tag model
public function products(): BelongsToMany
{
    return $this->belongsToMany(Product::class)->withTimestamps();
}

// Usage
$product->tags()->attach([1, 2, 3]);
$product->tags()->detach([2]);
$product->tags()->sync([1, 3, 5]);       // replaces all
$product->tags()->syncWithoutDetaching([4]); // adds without removing
```

### 4. Has-Through: hasOneThrough / hasManyThrough

```php
// Country -> User -> Post
// Country model
public function posts(): HasManyThrough
{
    return $this->hasManyThrough(Post::class, User::class);
}

// Access all posts from a country without manually joining
$country->posts;
```

### 5. Polymorphic: morphOne / morphMany / morphTo

```php
// Migration: create_comments_table
$table->id();
$table->morphs('commentable'); // creates commentable_type and commentable_id columns
$table->foreignId('user_id')->constrained();
$table->text('body');
$table->timestamps();

// Post model
public function comments(): MorphMany
{
    return $this->morphMany(Comment::class, 'commentable');
}

// Video model
public function comments(): MorphMany
{
    return $this->morphMany(Comment::class, 'commentable');
}

// Comment model
public function commentable(): MorphTo
{
    return $this->morphTo();
}

// Register morph map in AppServiceProvider::boot()
Relation::enforceMorphMap([
    'post' => Post::class,
    'video' => Video::class,
]);
```

### 6. Many-to-Many Polymorphic: morphToMany / morphedByMany

```php
// Migration: create_taggables_table
$table->id();
$table->foreignId('tag_id')->constrained()->cascadeOnDelete();
$table->morphs('taggable');
$table->unique(['tag_id', 'taggable_type', 'taggable_id']);

// Post model
public function tags(): MorphToMany
{
    return $this->morphToMany(Tag::class, 'taggable');
}

// Video model
public function tags(): MorphToMany
{
    return $this->morphToMany(Tag::class, 'taggable');
}

// Tag model
public function posts(): MorphedByMany
{
    return $this->morphedByMany(Post::class, 'taggable');
}

public function videos(): MorphedByMany
{
    return $this->morphedByMany(Video::class, 'taggable');
}
```

---

## Pivot Tables

### Extra Columns on Pivot

```php
// Migration
Schema::create('order_product', function (Blueprint $table) {
    $table->id();
    $table->foreignId('order_id')->constrained()->cascadeOnDelete();
    $table->foreignId('product_id')->constrained();
    $table->integer('quantity')->default(1);
    $table->decimal('unit_price', 10, 2);
    $table->timestamps();
    $table->unique(['order_id', 'product_id']);
});

// Order model
public function products(): BelongsToMany
{
    return $this->belongsToMany(Product::class)
        ->withPivot('quantity', 'unit_price')
        ->withTimestamps();
}

// Accessing pivot data
foreach ($order->products as $product) {
    echo $product->pivot->quantity;
    echo $product->pivot->unit_price;
}

// Attaching with extra data
$order->products()->attach($productId, [
    'quantity' => 2,
    'unit_price' => 29.99,
]);
```

### Custom Pivot Model

```php
// app/Models/OrderProduct.php
use Illuminate\Database\Eloquent\Relations\Pivot;

class OrderProduct extends Pivot
{
    protected $casts = [
        'unit_price' => 'decimal:2',
    ];

    public function getLineTotalAttribute(): float
    {
        return $this->quantity * $this->unit_price;
    }
}

// Order model
public function products(): BelongsToMany
{
    return $this->belongsToMany(Product::class)
        ->using(OrderProduct::class)
        ->withPivot('quantity', 'unit_price')
        ->withTimestamps();
}
```

---

## Soft Deletes

### Setup

```php
// Migration
$table->softDeletes(); // adds deleted_at TIMESTAMP NULLABLE

// Model
use Illuminate\Database\Eloquent\SoftDeletes;

class Order extends Model
{
    use SoftDeletes;
}
```

### Query Behavior

```php
Order::all();              // excludes soft-deleted (default)
Order::withTrashed()->get(); // includes soft-deleted
Order::onlyTrashed()->get(); // only soft-deleted

$order->trashed();         // check if soft-deleted
$order->restore();         // restore soft-deleted record
$order->forceDelete();     // permanently delete
```

### Unique Constraint with Soft Deletes

A unique constraint on `email` will conflict with soft-deleted records. Solutions:

```php
// Option 1: Partial unique index (PostgreSQL only)
DB::statement('CREATE UNIQUE INDEX users_email_unique ON users (email) WHERE deleted_at IS NULL');

// Option 2: Include deleted_at in composite unique (MySQL)
$table->unique(['email', 'deleted_at']);

// Option 3: Nullify the unique field on soft delete
public static function booted(): void
{
    static::softDeleted(function (User $user) {
        $user->updateQuietly(['email' => $user->email . '::deleted::' . $user->id]);
    });
}
```

### When to Use Soft Deletes

Use soft deletes when:
- Records must be auditable (orders, invoices, financial data)
- Records might need to be restored
- Related data references the record and you need referential integrity

Avoid soft deletes when:
- Data is truly disposable (logs, temporary records)
- Table will grow very large and soft-deleted records add overhead
- You are using the table as a queue or buffer

---

## Database Transactions

### Automatic Transaction with Retry

```php
use Illuminate\Support\Facades\DB;

$order = DB::transaction(function () use ($request) {
    $order = Order::create([
        'user_id' => auth()->id(),
        'total' => 0,
    ]);

    foreach ($request->items as $item) {
        $product = Product::lockForUpdate()->findOrFail($item['product_id']);

        if ($product->stock < $item['quantity']) {
            throw new InsufficientStockException($product);
        }

        $product->decrement('stock', $item['quantity']);

        $order->items()->create([
            'product_id' => $product->id,
            'quantity' => $item['quantity'],
            'unit_price' => $product->price,
        ]);
    }

    $order->update(['total' => $order->items->sum(fn ($i) => $i->quantity * $i->unit_price)]);

    return $order;
}, attempts: 3); // retries on deadlock
```

### Manual Transaction Control

```php
DB::beginTransaction();

try {
    $order = Order::create([...]);
    $order->items()->createMany([...]);

    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}
```

### Savepoints (Nested Transactions)

```php
DB::transaction(function () {
    Order::create([...]);

    DB::transaction(function () {
        // This creates a savepoint
        // If this inner block fails, only this portion rolls back
        Payment::create([...]);
    });
});
```

---

## Query Optimization

### Eager Loading (Preventing N+1)

```php
// Bad: N+1 problem (1 query for orders + N queries for customers)
$orders = Order::all();
foreach ($orders as $order) {
    echo $order->customer->name; // lazy-loads customer each iteration
}

// Good: Eager load (2 queries total)
$orders = Order::with('customer')->get();

// Nested eager loading
$orders = Order::with('customer', 'items.product')->get();

// Constrained eager loading
$orders = Order::with(['items' => function ($query) {
    $query->where('quantity', '>', 1)->orderBy('unit_price', 'desc');
}])->get();

// Lazy eager loading (when you already have the collection)
$orders = Order::all();
$orders->load('customer');
```

### Prevent Lazy Loading in Development

```php
// app/Providers/AppServiceProvider.php
public function boot(): void
{
    Model::preventLazyLoading(! app()->isProduction());
}
```

This throws an exception whenever a relationship is lazy-loaded, forcing you to use
eager loading everywhere.

### Subquery Selects

```php
// Add computed data without loading the relationship
$users = User::query()
    ->addSelect([
        'last_order_at' => Order::select('created_at')
            ->whereColumn('user_id', 'users.id')
            ->latest()
            ->take(1),
    ])
    ->get();
```

### Aggregate Functions on Relationships

```php
// withCount
$orders = Order::withCount('items')->get();
echo $orders->first()->items_count;

// withSum, withAvg, withMin, withMax
$orders = Order::withSum('items', 'quantity')
    ->withAvg('items', 'unit_price')
    ->get();
echo $orders->first()->items_sum_quantity;
echo $orders->first()->items_avg_unit_price;
```

### Chunking Large Datasets

```php
// Process records in chunks (memory-efficient)
Order::where('status', 'pending')->chunk(500, function ($orders) {
    foreach ($orders as $order) {
        $order->processReminder();
    }
});

// Lazy collection (even more memory-efficient for iteration)
Order::where('status', 'pending')->lazy()->each(function ($order) {
    $order->processReminder();
});

// Chunk by ID (safer for updates)
Order::where('status', 'pending')->chunkById(500, function ($orders) {
    foreach ($orders as $order) {
        $order->update(['reminded_at' => now()]);
    }
});
```

### When to Use Raw Queries

Use raw queries sparingly and only when Eloquent cannot express the logic efficiently:

```php
// Complex aggregation
$stats = DB::select(<<<'SQL'
    SELECT
        DATE(created_at) as date,
        COUNT(*) as total_orders,
        SUM(total) as revenue,
        AVG(total) as avg_order_value
    FROM orders
    WHERE created_at >= ?
    GROUP BY DATE(created_at)
    ORDER BY date DESC
SQL, [now()->subDays(30)]);

// Using selectRaw within Eloquent
$orders = Order::query()
    ->selectRaw('DATE(created_at) as date, COUNT(*) as count')
    ->groupByRaw('DATE(created_at)')
    ->get();
```

### Database Query Logging

```php
// Enable query log in development
DB::enableQueryLog();

// ... run queries ...

dd(DB::getQueryLog()); // see all executed queries with bindings and time
```

### Useful Artisan Commands

```bash
# Run all pending migrations
php artisan migrate

# Rollback last batch
php artisan migrate:rollback

# Rollback all and re-run
php artisan migrate:fresh          # drops all tables, runs all migrations
php artisan migrate:fresh --seed   # also runs seeders

# Check migration status
php artisan migrate:status

# Generate a migration
php artisan make:migration create_orders_table

# Generate a model with migration, factory, seeder, controller, and form requests
php artisan make:model Order -mfscR
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bramato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
