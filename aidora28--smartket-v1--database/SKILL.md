---
name: database-skill
description: Gestión de migraciones y multi-tenancy sin romper nada Use when this capability is needed.
metadata:
  author: aidora28
---

# Database Skill - SmartKet ERP

## 🎯 Propósito

Esta skill garantiza el **manejo correcto de la arquitectura multi-tenant** database-per-client, facilitando migraciones, seeders y operaciones de base de datos sin mezclar datos.

## 🏗️ Arquitectura de Base de Datos

### Landlord DB (`smartket_admin_db`)
Base de datos administrativa centralizada.

**Tablas clave**:
- `users` - Dueños de negocios
- `tenants` - Info de clientes registrados
- `plans`, `subscriptions` - Gestión SaaS
- `modules` - Catálogo de funcionalidades
- `audit_events` - Auditoría global

### Tenant DBs (`smartket_cliente_xyz`)
Bases de datos aisladas por cliente.

**Tablas clave**:
- `staff` - Empleados del negocio
- `branches` - Sucursales
- `products` - Inventario
- `sales`, `orders` - Transacciones
- Módulo-específicas: `tables` (pollería), etc.

---

## 📋 Naming Conventions

### Tablas
- **Plural, snake_case**: `products`, `sale_items`, `cash_registers`
- **Inglés**: No `productos`, no mezclar

### Columnas
- **snake_case**: `created_at`, `tenant_id`, `is_active`
- **Foreign keys**: `[tabla_singular]_id` → `category_id`, `product_id`
- **Booleans**: Prefijo `is_` o `has_` → `is_active`, `has_stock`

### Timestamps
- **Siempre** incluir: `created_at`, `updated_at`
- **Soft deletes** si aplica: `deleted_at`

---

## 🛠️ Scripts de Gestión

### 1. Crear Base de Datos de Tenant
```bash
.\. agent\skills\database\scripts\create-tenant-db.ps1 -TenantSlug "polleria-el-hornito"
```

**Qué hace**:
- Crea DB `smartket_polleria_el_hornito`
- Crea usuario PostgreSQL
- Ejecuta migraciones tenant
- Seednea datos iniciales

### 2. Migrar Tenant Específico
```bash
cd smartket-api
php ..\.agent\skills\database\scripts\migrate-tenant.php --tenant=5
```

**Qué hace**: Ejecuta migraciones solo en la DB del tenant ID 5

### 3. Seed Tenant
```bash
cd smartket-api
php ..\.agent\skills\database\scripts\seed-tenant.php --tenant=5 --module=polleria
```

**Qué hace**: Puebla BD del tenant con datos de prueba

### 4. Backup Tenant
```bash
.\. agent\skills\database\scripts\backup-tenant.ps1 -TenantId 5
```

**Qué hace**: Respalda solo la BD de ese tenant

---

## 📚 Templates

### Migration Template
Ver: `.agent/skills/database/examples/migration-template.php`

```php
// Tenant migration
return new class extends Migration {
    public function up() {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->decimal('price', 10, 2);
            $table->integer('stock')->default(0);
            $table->boolean('is_active')->default(true);
            $table->timestamps();
            $table->softDeletes();
        });
    }
};
```

### Seeder Template
Ver: `.agent/skills/database/examples/seeder-template.php`

```php
class ProductSeeder extends Seeder {
    public function run() {
        Product::factory(50)->create();
    }
}
```

### Factory Template
Ver: `.agent/skills/database/examples/factory-template.php`

```php
class ProductFactory extends Factory {
    public function definition() {
        return [
            'name' => $this->faker->productName(),
            'price' => $this->faker->randomFloat(2, 10, 1000),
            'stock' => $this->faker->numberBetween(0, 100),
        ];
    }
}
```

---

## 🚨 Reglas Críticas

### 1. Nunca Mezclar Conexiones
```php
// ❌ INCORRECTO
User::create([...]); // Landlord
Product::create([...]); // Tenant - ¿Cuál conexión?

// ✅ CORRECTO
// En config/database.php definir conexiones
// En modelos:
class Product {
    protected $connection = 'tenant'; // Explícito
}
```

### 2. Migraciones Separadas
```
database/migrations/
├── landlord/     ← `php artisan migrate --database=landlord`
└── tenant/       ← `php artisan migrate --database=tenant`
```

### 3. Seeders Multi-Tenant Aware
```php
// Seed solo en tenant activo
Tenant::all()->each(function ($tenant) {
    $tenant->run(function () {
        $this->call(ProductSeeder::class);
    });
});
```

---

## 🔄 Workflow de Migración

### 1. Desarrollo Local
```bash
# 1. Crear migration
php artisan make:migration create_products_table

# 2. Definir en database/migrations/tenant/

# 3. Ejecutar en tenant de prueba
php artisan migrate --database=tenant --path=database/migrations/tenant

# 4. Verificar
php artisan tinker
Product::count()
```

### 2. Producción
```bash
# Con --force en production
php artisan migrate --database=tenant --force

# Rollback si falla
php artisan migrate:rollback --database=tenant
```

---

## 📖 Multi-Tenant Best Practices

### 1. Trait para Modelos Tenant
```php
trait UsesTenantConnection {
    protected $connection = 'tenant';
    
    public static function bootUsesTenantConnection() {
        // Auto-scope por tenant actual
    }
}
```

### 2. Scoped Queries
```php
// Siempre filtrar por tenant
Product::where('tenant_id', session('tenant_id'))->get();

// O mejor, usar global scope
```

### 3. Tests Multi-Tenant
```php
test('data is isolated between tenants', function () {
    $tenant1 = Tenant::factory()->create();
    $tenant2 = Tenant::factory()->create();
    
    $tenant1->run(function () {
        Product::create(['name' => 'Tenant 1 Product']);
    });
    
    $tenant2->run(function () {
        expect(Product::count())->toBe(0); // Aislado
    });
});
```

---

## 🎓 Cuándo Usar Esta Skill

✅ **Usar cuando**:
- Crees nuevas migraciones
- Agregues tenant
- Hagas seeding de datos
- Cambies esquema de DB
- Debuggees problemas de conexión

❌ **No para**:
- Queries simples en application code
- Cambios de datos (usa tinker o admin)

---

## 💡 Filosofía

> "En multi-tenancy, cada base de datos es un universo. No cruces universos sin intención."

Los datos de un cliente **NUNCA** deben verse afectados por operaciones de otro.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidora28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
