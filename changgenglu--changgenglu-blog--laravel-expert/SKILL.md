---
name: laravel-expert
description: name: "laravel-expert" Use when this capability is needed.
metadata:
  author: changgenglu
---
---
name: "laravel-expert"
description: "Activates when user requests Laravel framework guidance, version migration, Eloquent patterns, middleware design, service container usage, or Laravel best practices. Do NOT use for generic PHP questions unrelated to the framework. Examples: 'How to use Service Container?', 'Translate this to Laravel 12'."
---

# Laravel Expert Skill

## 🧠 Expertise

Laravel 框架專家，專精於跨版本開發、框架核心機制與最佳實務。

> **官方文檔參考**：https://laravel.com/docs/

---

## 1. 版本差異對照表

### 1.1 支援政策

| 版本 | PHP 版本 | 發布日期 | Bug 修復結束 | 安全修復結束 |
|-----|---------|---------|-------------|-------------|
| **9.x** | 8.0 - 8.2 | 2022-02 | 2023-08 | 2024-02 |
| **10.x** | 8.1 - 8.3 | 2023-02 | 2024-08 | 2025-02 |
| **11.x** | 8.2 - 8.4 | 2024-03 | 2025-09 | 2026-03 |
| **12.x** | 8.2+ | 2025-02 | 2026-08 | 2027-02 |

> **參考**：https://laravel.com/docs/12.x/releases#support-policy

### 1.2 核心差異對照

| 特性 | Laravel 9 | Laravel 11 | Laravel 12 |
|-----|-----------|------------|------------|
| **目錄結構** | 傳統完整 | 精簡化 | 精簡化 |
| **app/Http/Kernel.php** | ✅ 存在 | ❌ 移除 | ❌ 移除 |
| **Middleware 註冊** | Kernel | bootstrap/app.php | bootstrap/app.php |
| **Exception Handler** | app/Exceptions | bootstrap/app.php | bootstrap/app.php |
| **預設測試框架** | PHPUnit | Pest 可選 | Pest 預設 |
| **Model Casts** | `$casts` 屬性 | `casts()` 方法 | `casts()` 方法 |
| **前端工具** | Mix | Vite | Vite |
| **Starter Kits** | Breeze/Jetstream | Breeze/Jetstream | 新 Starter Kits |

### 1.3 Laravel 9 主要特性

```php
// 新版 Accessor / Mutator 語法
use Illuminate\Database\Eloquent\Casts\Attribute;

protected function name(): Attribute
{
    return Attribute::make(
        get: fn (string $value) => ucfirst($value),
        set: fn (string $value) => strtolower($value),
    );
}

// Enum Casting
protected $casts = [
    'status' => OrderStatus::class,
];

// Controller Route Groups
Route::controller(OrderController::class)->group(function () {
    Route::get('/orders', 'index');
    Route::post('/orders', 'store');
});
```

### 1.4 Laravel 11/12 精簡化結構

```php
// bootstrap/app.php（Laravel 11+）
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
    )
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->web(append: [
            CheckUserStatus::class,
        ]);
    })
    ->withExceptions(function (Exceptions $exceptions) {
        $exceptions->render(function (NotFoundHttpException $e) {
            return response()->json(['error' => 'Not found'], 404);
        });
    })
    ->create();
```

---

## 2. Service Container 與依賴注入

### 2.1 基本綁定

```php
// 綁定介面到實作
$this->app->bind(IPaymentGateway::class, StripeGateway::class);

// 單例綁定
$this->app->singleton(IPaymentGateway::class, StripeGateway::class);

// 實例綁定
$this->app->instance(IPaymentGateway::class, $gateway);

// 上下文綁定
$this->app->when(OrderService::class)
    ->needs(IPaymentGateway::class)
    ->give(StripeGateway::class);
```

### 2.2 自動解析

```php
// 自動注入（Constructor Injection）
class OrderController extends Controller
{
    public function __construct(
        private readonly IOrderService $orderService,
        private readonly IPaymentGateway $paymentGateway,
    ) {}
}

// 方法注入
public function store(Request $request, IOrderService $service)
{
    $order = $service->createOrder($request->validated());
}
```

---

## 3. Middleware 設計

### 3.1 Laravel 9 方式（Kernel）

```php
// app/Http/Kernel.php
protected $middlewareGroups = [
    'web' => [
        // ...
    ],
    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];

protected $middlewareAliases = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

### 3.2 Laravel 11+ 方式（bootstrap/app.php）

```php
->withMiddleware(function (Middleware $middleware) {
    // 新增到 web 群組
    $middleware->web(append: [
        CheckUserStatus::class,
    ]);
    
    // 新增到 api 群組
    $middleware->api(prepend: [
        EnsureTokenIsValid::class,
    ]);
    
    // 別名
    $middleware->alias([
        'admin' => EnsureUserIsAdmin::class,
    ]);
    
    // 全域 Middleware
    $middleware->append(LogRequests::class);
})
```

### 3.3 自訂 Middleware

```php
class EnsureUserIsActive
{
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->user()?->status !== 'active') {
            abort(403, 'Your account is not active.');
        }
        
        return $next($request);
    }
}
```

---

## 4. Eloquent 進階模式

### 4.1 Query Scope

```php
// Local Scope
public function scopeActive(Builder $query): void
{
    $query->where('status', 'active');
}

public function scopeOfType(Builder $query, string $type): void
{
    $query->where('type', $type);
}

// 使用
User::active()->ofType('admin')->get();

// Global Scope（自動套用）
class ActiveScope implements Scope
{
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('deleted_at', null);
    }
}
```

### 4.2 Accessor & Mutator

```php
// Laravel 9+ 新語法
use Illuminate\Database\Eloquent\Casts\Attribute;

protected function fullName(): Attribute
{
    return Attribute::make(
        get: fn () => "{$this->first_name} {$this->last_name}",
    );
}

protected function password(): Attribute
{
    return Attribute::make(
        set: fn (string $value) => Hash::make($value),
    );
}
```

### 4.3 Custom Casts

```php
class MoneyCast implements CastsAttributes
{
    public function get(Model $model, string $key, mixed $value, array $attributes): Money
    {
        return new Money($value, $attributes['currency'] ?? 'USD');
    }
    
    public function set(Model $model, string $key, mixed $value, array $attributes): int
    {
        return $value instanceof Money ? $value->cents : $value;
    }
}

// 使用
protected function casts(): array
{
    return [
        'price' => MoneyCast::class,
    ];
}
```

---

## 5. Event / Listener / Observer

### 5.1 Event 與 Listener

```php
// 定義 Event
class OrderPlaced
{
    public function __construct(
        public readonly Order $order,
    ) {}
}

// 定義 Listener
class SendOrderConfirmation
{
    public function handle(OrderPlaced $event): void
    {
        Mail::to($event->order->user)->send(
            new OrderConfirmationMail($event->order)
        );
    }
}

// 觸發
event(new OrderPlaced($order));
// 或
OrderPlaced::dispatch($order);
```

### 5.2 Observer

```php
class UserObserver
{
    public function created(User $user): void
    {
        Log::info("User created: {$user->id}");
    }
    
    public function updated(User $user): void
    {
        if ($user->isDirty('email')) {
            $user->email_verified_at = null;
        }
    }
    
    public function deleted(User $user): void
    {
        $user->orders()->delete();
    }
}

// 註冊（AppServiceProvider）
User::observe(UserObserver::class);
```

---

## 6. Form Request 驗證

### 6.1 基本用法

```php
class StoreOrderRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Order::class);
    }
    
    public function rules(): array
    {
        return [
            'product_id' => ['required', 'exists:products,id'],
            'quantity' => ['required', 'integer', 'min:1', 'max:100'],
            'notes' => ['nullable', 'string', 'max:500'],
        ];
    }
    
    public function messages(): array
    {
        return [
            'product_id.exists' => '商品不存在',
            'quantity.min' => '數量至少為 1',
        ];
    }
}
```

### 6.2 進階驗證

```php
public function rules(): array
{
    return [
        'email' => [
            'required',
            'email',
            Rule::unique('users')->ignore($this->user),
        ],
        'role' => [
            'required',
            Rule::in(['admin', 'user', 'guest']),
        ],
        'status' => [
            'required',
            Rule::enum(UserStatus::class),
        ],
    ];
}

// 條件驗證
public function withValidator(Validator $validator): void
{
    $validator->sometimes('phone', 'required', function ($input) {
        return $input->contact_method === 'phone';
    });
}
```

---

## 7. Policy / Gate 權限

### 7.1 Policy 定義

```php
class OrderPolicy
{
    public function view(User $user, Order $order): bool
    {
        return $user->id === $order->user_id;
    }
    
    public function update(User $user, Order $order): bool
    {
        return $user->id === $order->user_id 
            && $order->status === 'pending';
    }
    
    public function delete(User $user, Order $order): bool
    {
        return $user->isAdmin();
    }
}

// 使用
$this->authorize('update', $order);
// 或
Gate::authorize('update', $order);
// 或
if ($user->can('update', $order)) { }
```

### 7.2 Gate 定義

```php
// AuthServiceProvider 或 AppServiceProvider
Gate::define('access-admin', function (User $user) {
    return $user->role === 'admin';
});

Gate::define('edit-settings', function (User $user) {
    return $user->isAdmin();
});

// 使用
if (Gate::allows('access-admin')) { }
@can('access-admin') ... @endcan
```

---

## 8. 升級遷移指南

### 8.1 Laravel 9 → 10

| 變更項目 | 處理方式 |
|---------|---------|
| PHP 8.1 最低版本 | 升級 PHP |
| PHPUnit 10 | 更新測試配置 |
| Pest 2 | 更新 Pest |

### 8.2 Laravel 10 → 11

| 變更項目 | 處理方式 |
|---------|---------|
| 目錄結構精簡化 | 可選擇保留或遷移 |
| Kernel.php 移除 | 遷移到 bootstrap/app.php |
| Exception Handler 移除 | 遷移到 bootstrap/app.php |
| Model casts() 方法 | 可選擇遷移 |

### 8.3 Laravel 11 → 12

> **預計升級時間**：5 分鐘（官方建議）
> **參考**：https://laravel.com/docs/12.x/upgrade

| 影響程度 | 項目 |
|---------|------|
| **高** | 更新依賴 (`laravel/framework` ^12.0) |
| **中** | Model UUIDv7 變更 |
| **低** | Carbon 3、Image 驗證排除 SVG |

```bash
# 升級命令
composer require laravel/framework:^12.0 phpunit/phpunit:^11.0 pestphp/pest:^3.0
```

---

## 9. 常用 Artisan 命令

```bash
# 開發
php artisan serve
php artisan tinker
php artisan route:list

# 快取
php artisan optimize
php artisan optimize:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 資料庫
php artisan migrate
php artisan migrate:fresh --seed
php artisan db:seed

# 產生器
php artisan make:model Order -mfsc  # Model + Migration + Factory + Seeder + Controller
php artisan make:request StoreOrderRequest
php artisan make:policy OrderPolicy --model=Order
php artisan make:event OrderPlaced
php artisan make:listener SendOrderNotification --event=OrderPlaced
```

---

## 10. Laravel 檢查清單

### 版本相容
- [ ] PHP 版本是否符合要求？
- [ ] 依賴套件是否支援目標 Laravel 版本？
- [ ] 是否有廢棄 API 使用？

### 架構設計
- [ ] 是否使用 Service 層處理業務邏輯？
- [ ] 是否使用 Repository 模式？
- [ ] 是否正確使用依賴注入？
- [ ] 是否為高風險操作設定 Policy？

### 效能
- [ ] 是否使用 Eager Loading？
- [ ] 是否設定適當快取？
- [ ] 是否使用 Queue 處理耗時任務？

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/changgenglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
