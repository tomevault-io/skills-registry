---
name: database-seeding
description: Idempotent seeder patterns with core/reference/dev separation and environment-specific execution using firstOrCreate/updateOrCreate Use when this capability is needed.
metadata:
  author: hackur
---

# Database Seeding

Create and manage idempotent database seeders following PCR Card's core/reference/dev pattern.

## When to Use

- Creating new seeders
- Resetting database with test data
- Seeding specific data sets (core, reference, development)
- Understanding seeder organization
- Troubleshooting seeding issues

## Quick Commands

```bash
# Full database reset with all seeders
./scripts/dev.sh fresh

# Core seeders only (all environments)
./scripts/dev.sh seed:core

# Reference data (trading card catalog)
./scripts/dev.sh seed:reference

# Development test data
./scripts/dev.sh seed:dev

# Specific seeder class
php artisan db:seed --class=MenuSeeder
php artisan db:seed --class=Database\\Seeders\\Development\\DuskTestSeeder
```

## Idempotent Pattern

**Critical Rule**: All seeders must be **safe to run multiple times**.

### Use firstOrCreate() or updateOrCreate()

```php
// ✅ CORRECT: Idempotent (safe to re-run)
User::firstOrCreate(
    ['email' => 'admin@pcrcard.com'],
    [
        'name' => 'PCR Card Admin',
        'password' => Hash::make('admin123!'),
    ]
);

// ✅ CORRECT: Updates existing records
Service::updateOrCreate(
    ['service_code' => 'BASE_RESTORATION'],
    [
        'name' => 'Base Restoration',
        'base_price' => 3500,
        'is_active' => true,
    ]
);
```

### ❌ NEVER Use create()

```php
// ❌ WRONG: Not idempotent (fails on second run)
User::create([
    'email' => 'admin@pcrcard.com',
    'name' => 'PCR Card Admin',
    'password' => Hash::make('admin123!'),
]);
// Second run: SQLSTATE[23000]: Integrity constraint violation
```

## Seeder Organization

### Three Categories

| Category | Purpose | When to Run | Example |
|----------|---------|-------------|---------|
| **Core** | Essential data for all environments | Always | Roles, Permissions, Services, Admin User |
| **Reference** | Static catalog data | Optional | Trading card sets, card catalog |
| **Development** | Test data for local development | Local only | Dusk test users, sample submissions |

### Directory Structure

```
database/seeders/
├── DatabaseSeeder.php           # Main orchestrator
├── Core/                        # Core seeders (run in all envs)
│   ├── CoreRolesAndPermissionsSeeder.php
│   ├── CoreServicesSeeder.php
│   ├── CoreAdminUserSeeder.php
│   └── MenuSeeder.php
├── Reference/                   # Reference data (optional)
│   ├── TradingCardSetsSeeder.php
│   └── TradingCardsSeeder.php
└── Development/                 # Dev-only test data
    ├── DuskTestSeeder.php
    ├── DevSubmissionsSeeder.php
    └── DevSubmissionImagesSeeder.php
```

## Core Seeders

### Always Run (All Environments)

**1. CoreRolesAndPermissionsSeeder**
- 3 roles: Admin, Technician, Customer
- 39 permissions across 11 resources
- Uses Spatie Permission package

**2. CoreServicesSeeder**
- 10 baseline services (restoration types, addons, shipping)
- Base prices and service codes
- Active/inactive status

**3. CoreAdminUserSeeder**
- admin@pcrcard.com with Admin + Technician roles
- Password: admin123! (development)
- Email verified, beta access granted

**4. MenuSeeder**
- 29 Nova admin menu items
- 5 groups: Dashboards, Library, Workflow, Payments, System
- Hierarchical structure (3 levels)

**Run via**:

```bash
./scripts/dev.sh seed:core
# Or manually:
php artisan db:seed --class=Database\\Seeders\\Core\\CoreRolesAndPermissionsSeeder
php artisan db:seed --class=Database\\Seeders\\Core\\CoreServicesSeeder
php artisan db:seed --class=Database\\Seeders\\Core\\CoreAdminUserSeeder
php artisan db:seed --class=MenuSeeder
```

## Reference Data Seeders

### Optional Catalog Data

**TradingCardSetsSeeder** - Card set catalog
**TradingCardsSeeder** - Individual card catalog

**When to use**: If you need realistic trading card data for testing submissions

**Run via**:

```bash
./scripts/dev.sh seed:reference
```

## Development Seeders

### Test Data (Local Only)

**DuskTestSeeder** - Browser test users:
- dusk.approved@test.com (verified + beta)
- dusk.customer@test.com (verified, no beta)
- dusk.unverified@test.com (not verified, no beta)
- dusk.admin@test.com (admin role)
- dusk.tech@test.com (technician role)

**DevSubmissionsSeeder** - Sample submissions with cards

**DevSubmissionImagesSeeder** - Submission-level images:
- Package arrival photos
- Batch photography
- Shipping documentation

**Run via**:

```bash
./scripts/dev.sh seed:dev
```

## Seeder Output Format

### Structured Summary

After seeding, `DatabaseSeeder` provides:

```
╔═══════════════════════════════════════════════════════════════╗
║                 DEVELOPMENT ENVIRONMENT SEEDING                ║
╔═══════════════════════════════════════════════════════════════╝

📋 CORE SEEDERS (Always Run)
  ✅ Roles & Permissions
  ✅ Services
  ✅ Admin User
  ✅ Nova Menus

📚 REFERENCE DATA (Included)
  ✅ Trading Card Sets
  ✅ Trading Cards

🔧 DEVELOPMENT SEEDERS (Environment-Specific)
  ✅ Dusk Test Users
  ✅ Sample Submissions
  ✅ Submission Images

✅ Seeding completed successfully!

📝 MANUAL COMMANDS AVAILABLE:
  php artisan db:seed --class=Database\\Seeders\\Development\\DuskTestSeeder
  php artisan db:seed --class=Database\\Seeders\\Development\\DevSubmissionsSeeder
```

## Environment-Specific Execution

### DatabaseSeeder Logic

```php
public function run(): void
{
    // CORE: Always run
    $this->call([
        CoreRolesAndPermissionsSeeder::class,
        CoreServicesSeeder::class,
        CoreAdminUserSeeder::class,
        MenuSeeder::class,
    ]);

    // REFERENCE: Optional (flag-controlled)
    if ($this->includeReferenceData()) {
        $this->call([
            TradingCardSetsSeeder::class,
            TradingCardsSeeder::class,
        ]);
    }

    // DEVELOPMENT: Local only
    if (App::environment('local')) {
        $this->call([
            DuskTestSeeder::class,
            DevSubmissionsSeeder::class,
            DevSubmissionImagesSeeder::class,
        ]);
    }

    // STAGING: Staging-specific seeders
    if (App::environment('staging')) {
        // Staging seeders here
    }

    // PRODUCTION: Production-specific seeders
    if (App::environment('production')) {
        // Production seeders here (usually none)
    }
}
```

## Creating New Seeders

### Template

```php
<?php

namespace Database\Seeders\Core; // or Development, Reference

use App\Models\YourModel;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\Hash;

class YourSeeder extends Seeder
{
    public function run(): void
    {
        // Use firstOrCreate for unique records
        YourModel::firstOrCreate(
            ['unique_field' => 'value'],
            [
                'other_field' => 'value',
                'created_at' => now(),
            ]
        );

        // Use updateOrCreate to update existing
        YourModel::updateOrCreate(
            ['unique_field' => 'value'],
            [
                'other_field' => 'new_value',
                'updated_at' => now(),
            ]
        );

        // For multiple records
        $records = [
            ['name' => 'Record 1', 'code' => 'REC1'],
            ['name' => 'Record 2', 'code' => 'REC2'],
        ];

        foreach ($records as $record) {
            YourModel::updateOrCreate(
                ['code' => $record['code']],
                $record
            );
        }

        $this->command->info('✅ YourModel seeded successfully');
    }
}
```

### Artisan Command

```bash
# Create seeder
php artisan make:seeder YourSeeder

# Move to appropriate directory
mv database/seeders/YourSeeder.php database/seeders/Core/
# or Development/, Reference/
```

## Common Patterns

### Seeding with Relationships

```php
// Create parent with children
$submission = Submission::firstOrCreate(
    ['submission_number' => 'SUB-001'],
    ['user_id' => $user->id, 'status' => 'draft']
);

// Attach related models
$submission->cards()->firstOrCreate(
    ['card_number' => 1],
    ['description' => 'Card 1']
);
```

### Seeding with Factories (Development Only)

```php
// In Development seeders, you can use factories
if (App::environment('local')) {
    User::factory()->count(10)->create();
}
```

### Conditional Seeding

```php
// Only seed if table is empty
if (Service::count() === 0) {
    // Seed services
}

// Or use firstOrCreate (handles this automatically)
```

## Common Pitfalls

### ❌ WRONG: Using create() instead of firstOrCreate()

```php
Role::create(['name' => 'Admin']);
// Second run: duplicate key error
```

**✅ CORRECT**:

```php
Role::firstOrCreate(['name' => 'Admin']);
// Second run: no error, returns existing record
```

### ❌ WRONG: Not specifying unique keys

```php
Service::updateOrCreate([
    'name' => 'Base Restoration',
    'base_price' => 3500,
]);
// Creates duplicate if name changed
```

**✅ CORRECT**: Use service_code as unique identifier

```php
Service::updateOrCreate(
    ['service_code' => 'BASE_RESTORATION'],
    [
        'name' => 'Base Restoration',
        'base_price' => 3500,
    ]
);
```

### ❌ WRONG: Hardcoding IDs

```php
Submission::create([
    'user_id' => 1,  // Assumes user ID is 1
]);
```

**✅ CORRECT**: Look up by unique field

```php
$user = User::where('email', 'admin@pcrcard.com')->first();
Submission::firstOrCreate([
    'submission_number' => 'SUB-001',
    'user_id' => $user->id,
]);
```

## Testing Seeders

### Verify Idempotence

```bash
# Run seeder twice
php artisan db:seed --class=YourSeeder
php artisan db:seed --class=YourSeeder

# Should complete without errors
# Check record count hasn't doubled
php artisan tinker
>>> YourModel::count();
```

### Reset and Reseed

```bash
# Full reset
./scripts/dev.sh fresh

# Check all seeders ran successfully
php artisan tinker
>>> User::where('email', 'admin@pcrcard.com')->exists();
=> true
>>> Service::count();
=> 10
>>> MenuItem::count();
=> 29
```

## Documentation Links

- **Database Seeders Guide**: `docs/DATABASE-SEEDERS.md`
- **Service Architecture**: `docs/SERVICE-ARCHITECTURE.md`
- **Nova Menus Guide**: `docs/features/NOVA-MENUS-GUIDE.md`
- **Submission Images Guide**: `docs/features/SUBMISSION-IMAGES-GUIDE.md`
- **Laravel Seeding**: https://laravel.com/docs/12.x/seeding

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hackur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
