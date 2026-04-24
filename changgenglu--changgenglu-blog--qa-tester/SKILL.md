---
name: qa-tester
description: description: "Activates when user requests test strategy design, automated testing frameworks, test case design, load testing, or security testing. Do NOT use for fixing bugs in production code. Examples: 'Write PHPUnit tests', 'Design test cases for login'." Use when this capability is needed.
metadata:
  author: changgenglu
---
---
name: "qa-tester"
description: "Activates when user requests test strategy design, automated testing frameworks, test case design, load testing, or security testing. Do NOT use for fixing bugs in production code. Examples: 'Write PHPUnit tests', 'Design test cases for login'."
---

# QA Tester Skill

## 🧠 Expertise

資深品質保證工程師，專精於自動化測試、測試策略設計與軟體品質管理。

---

## 1. 測試策略設計

### 1.1 測試金字塔

```
          ┌─────────┐
          │  E2E    │  ← 少量：驗證關鍵流程
         ┌┴─────────┴┐
         │Integration│  ← 中量：驗證模組整合
        ┌┴───────────┴┐
        │    Unit     │  ← 大量：驗證單一函數
        └─────────────┘
```

| 層級 | 比例 | 執行速度 | 維護成本 |
|-----|------|---------|---------|
| **Unit** | 70% | 快 | 低 |
| **Integration** | 20% | 中 | 中 |
| **E2E** | 10% | 慢 | 高 |

### 1.2 測試類型矩陣

| 類型 | 目的 | 工具 |
|-----|------|------|
| **功能測試** | 驗證業務邏輯 | PHPUnit, Jest |
| **整合測試** | 驗證模組協作 | Pest, Cypress |
| **效能測試** | 驗證系統負載 | k6, JMeter |
| **安全測試** | 驗證安全防護 | OWASP ZAP |
| **回歸測試** | 驗證修改影響 | 自動化套件 |

---

## 2. 單元測試設計

### 2.1 測試結構 (AAA Pattern)

```php
public function testUserCanPlaceOrder(): void
{
    // Arrange - 準備測試資料
    $user = User::factory()->create(['balance' => 1000]);
    $product = Product::factory()->create(['price' => 100]);
    
    // Act - 執行被測試行為
    $order = $this->orderService->placeOrder($user, $product);
    
    // Assert - 驗證結果
    $this->assertEquals(Order::STATUS_CREATED, $order->status);
    $this->assertEquals(900, $user->fresh()->balance);
}
```

### 2.2 測試命名規範

```php
// 格式：test_[行為]_[條件]_[預期結果]
public function test_user_registration_with_valid_data_creates_account(): void
public function test_login_with_wrong_password_returns_error(): void
public function test_order_with_insufficient_balance_throws_exception(): void
```

### 2.3 Mock 與 Stub

```php
// Mock 外部服務
$this->mock(PaymentGateway::class, function ($mock) {
    $mock->shouldReceive('charge')
         ->once()
         ->with(100.00, 'card_token')
         ->andReturn(new PaymentResult(true));
});

// Fake 資料庫
$this->fake(Event::class);
$this->fake(Notification::class);
```

---

## 3. 整合測試設計

### 3.1 API 測試

```php
public function test_create_order_api(): void
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)
        ->postJson('/api/orders', [
            'product_id' => 1,
            'quantity' => 2,
        ]);
    
    $response->assertStatus(201)
             ->assertJsonStructure([
                 'data' => ['id', 'status', 'total'],
             ]);
    
    $this->assertDatabaseHas('orders', [
        'user_id' => $user->id,
        'status' => 'pending',
    ]);
}
```

### 3.2 資料庫測試

```php
use RefreshDatabase;  // 每個測試重建資料庫
use DatabaseTransactions;  // 每個測試回滾

protected function setUp(): void
{
    parent::setUp();
    $this->seed(TestDataSeeder::class);
}
```

---

## 4. 安全測試

### 4.1 注入攻擊測試

```php
public function test_sql_injection_prevention(): void
{
    $maliciousInputs = [
        "'; DROP TABLE users; --",
        "1' OR '1'='1",
        "admin'/*",
    ];
    
    foreach ($maliciousInputs as $input) {
        $response = $this->postJson('/api/login', [
            'username' => $input,
            'password' => 'password',
        ]);
        
        $response->assertStatus(422);  // 驗證錯誤，非 SQL 錯誤
    }
    
    $this->assertDatabaseCount('users', $originalCount);
}
```

### 4.2 XSS 測試

```php
public function test_xss_prevention(): void
{
    $xssPayloads = [
        '<script>alert("XSS")</script>',
        '"><script>alert(document.cookie)</script>',
        '<img src=x onerror=alert("XSS")>',
    ];
    
    foreach ($xssPayloads as $payload) {
        $response = $this->putJson('/api/profile', [
            'nickname' => $payload,
        ]);
        
        $response->assertStatus(422);
    }
}
```

### 4.3 授權測試

```php
public function test_user_cannot_access_other_user_data(): void
{
    $user1 = User::factory()->create();
    $user2 = User::factory()->create();
    
    $response = $this->actingAs($user1)
        ->getJson("/api/users/{$user2->id}/orders");
    
    $response->assertStatus(403);  // 或 404 隱藏存在性
}
```

---

## 5. 效能測試

### 5.1 負載測試腳本 (k6)

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    stages: [
        { duration: '1m', target: 100 },   // 漸進增加
        { duration: '5m', target: 100 },   // 維持負載
        { duration: '1m', target: 0 },     // 漸進減少
    ],
    thresholds: {
        http_req_duration: ['p(95)<500'],  // 95% 請求 < 500ms
        http_req_failed: ['rate<0.01'],    // 錯誤率 < 1%
    },
};

export default function () {
    const res = http.get('https://api.example.com/products');
    
    check(res, {
        'status is 200': (r) => r.status === 200,
        'response time < 500ms': (r) => r.timings.duration < 500,
    });
    
    sleep(1);
}
```

### 5.2 效能指標

| 指標 | 說明 | 目標值 |
|-----|------|-------|
| **Throughput** | 每秒請求數 | > 1000 RPS |
| **Latency P95** | 95% 請求延遲 | < 200ms |
| **Error Rate** | 錯誤請求比例 | < 0.1% |
| **Apdex** | 應用效能指數 | > 0.9 |

---

## 6. 測試資料管理

### 6.1 Factory 設計

```php
class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'status' => 'active',
        ];
    }
    
    public function suspended(): static
    {
        return $this->state(['status' => 'suspended']);
    }
    
    public function withOrders(int $count = 3): static
    {
        return $this->has(Order::factory()->count($count));
    }
}

// 使用
User::factory()->suspended()->withOrders(5)->create();
```

### 6.2 測試資料隔離

```php
// 每個測試使用獨立 tenant
protected function setUp(): void
{
    parent::setUp();
    $this->tenant = Tenant::factory()->create();
    $this->app->instance('current_tenant', $this->tenant);
}
```

---

## 7. 測試品質指標

### 7.1 覆蓋率目標

| 類型 | 目標 |
|-----|------|
| **行覆蓋率** | > 80% |
| **分支覆蓋率** | > 70% |
| **關鍵路徑** | 100% |

### 7.2 測試檢查清單

- [ ] 正常流程是否覆蓋？
- [ ] 邊界條件是否測試？
- [ ] 異常情況是否處理？
- [ ] 並發場景是否驗證？
- [ ] 安全漏洞是否檢測？

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/changgenglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
