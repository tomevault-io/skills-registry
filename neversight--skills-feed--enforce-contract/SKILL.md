---
name: enforce-contract
description: 單元測試與代碼提交前觸發。掃描並驗證方法的 pre-conditions、post-conditions 與 invariants，透過契約式設計減少 AI 幻覺。 Use when this capability is needed.
metadata:
  author: neversight
---

# Enforce Contract Skill

## 觸發時機

- 編寫單元測試前
- 實作 `analyze-frame` 產出的規格時
- 代碼提交（commit）前
- 實作新方法時
- AI 生成代碼後的驗證

## 核心任務

透過 Design by Contract 明確定義每個方法的邊界條件，極大化減少 AI 幻覺。

## 契約式設計三要素

### 1. Pre-conditions（前置條件）
- **定義**：呼叫方法前必須滿足的條件
- **責任歸屬**：呼叫者 (Caller) 的責任
- **違反時**：方法可以拒絕執行

### 2. Post-conditions（後置條件）
- **定義**：方法執行完畢後保證成立的條件
- **責任歸屬**：被呼叫者 (Callee) 的責任
- **違反時**：表示方法實作有 bug

### 3. Invariants（不變量）
- **定義**：物件生命週期內始終成立的條件
- **適用時機**：任何公開方法呼叫前後
- **違反時**：表示物件狀態已損壞

## 契約標註格式

### 使用 Javadoc 標註

```java
/**
 * 建立新訂單
 * 
 * @param input 建立訂單的輸入參數
 * @return 建立成功的訂單資訊
 * 
 * @pre input != null
 * @pre input.getCustomerId() != null
 * @pre input.getItems() != null && !input.getItems().isEmpty()
 * @pre 所有 items 的 quantity > 0
 * @pre 所有 items 的 productId 對應的商品存在
 * 
 * @post result != null
 * @post result.getOrderId() != null
 * @post result.getStatus() == OrderStatus.CREATED
 * @post 訂單已持久化到資料庫
 * @post OrderCreatedEvent 已發布
 * 
 * @throws CustomerNotFoundException 當 customerId 對應的客戶不存在
 * @throws ProductNotFoundException 當 productId 對應的商品不存在
 * @throws InsufficientInventoryException 當庫存不足
 */
public Output execute(Input input) {
    // 實作
}
```

### 使用程式碼驗證 Pre-conditions

```java
public Output execute(Input input) {
    // ===== Pre-conditions =====
    Objects.requireNonNull(input, "input must not be null");
    Objects.requireNonNull(input.getCustomerId(), "customerId must not be null");
    
    if (input.getItems() == null || input.getItems().isEmpty()) {
        throw new IllegalArgumentException("items must not be empty");
    }
    
    for (OrderItemRequest item : input.getItems()) {
        if (item.getQuantity() <= 0) {
            throw new IllegalArgumentException(
                "quantity must be positive, got: " + item.getQuantity()
            );
        }
    }
    
    // ===== 主要邏輯 =====
    // ...
    
    // ===== Post-conditions (assert in development) =====
    assert result != null : "result must not be null";
    assert result.getOrderId() != null : "orderId must not be null";
    
    return result;
}
```

## Entity/Aggregate 的 Invariants

### 範例：Order Aggregate

```java
public class Order {
    private OrderId id;
    private CustomerId customerId;
    private List<OrderItem> items;
    private OrderStatus status;
    private Money totalAmount;
    
    /**
     * Order 的不變量：
     * @invariant id != null
     * @invariant customerId != null
     * @invariant items != null && !items.isEmpty()
     * @invariant totalAmount != null && totalAmount.isPositive()
     * @invariant status != null
     * @invariant 當 status == CANCELLED 時，不能再修改訂單內容
     */
    
    // 建構子必須建立有效狀態
    public Order(OrderId id, CustomerId customerId, List<OrderItem> items) {
        // Pre-conditions
        Objects.requireNonNull(id, "id must not be null");
        Objects.requireNonNull(customerId, "customerId must not be null");
        if (items == null || items.isEmpty()) {
            throw new IllegalArgumentException("items must not be empty");
        }
        
        this.id = id;
        this.customerId = customerId;
        this.items = new ArrayList<>(items);
        this.status = OrderStatus.CREATED;
        this.totalAmount = calculateTotal();
        
        // 驗證 invariants
        assertInvariants();
    }
    
    public void addItem(OrderItem item) {
        // Pre-conditions
        Objects.requireNonNull(item, "item must not be null");
        if (this.status == OrderStatus.CANCELLED) {
            throw new IllegalStateException("Cannot modify cancelled order");
        }
        
        // 執行變更
        this.items.add(item);
        this.totalAmount = calculateTotal();
        
        // Post-conditions & Invariants
        assertInvariants();
    }
    
    public void cancel() {
        // Pre-conditions
        if (this.status == OrderStatus.SHIPPED) {
            throw new IllegalStateException("Cannot cancel shipped order");
        }
        
        // 執行變更
        this.status = OrderStatus.CANCELLED;
        
        // Invariants
        assertInvariants();
    }
    
    private void assertInvariants() {
        assert id != null : "Invariant violated: id is null";
        assert customerId != null : "Invariant violated: customerId is null";
        assert items != null && !items.isEmpty() : "Invariant violated: items is empty";
        assert totalAmount != null && totalAmount.isPositive() : 
            "Invariant violated: totalAmount is invalid";
        assert status != null : "Invariant violated: status is null";
    }
}
```

## 契約掃描檢查項目

### 必須檢查的項目

| 項目 | 描述 | 嚴重度 |
|------|------|--------|
| Null Check | 所有物件參數是否有 null 檢查 | 🔴 嚴重 |
| Empty Collection | 集合參數是否檢查 empty | 🟡 中度 |
| Positive Numbers | 數量、金額等是否檢查正數 | 🟡 中度 |
| Valid State | 狀態轉換是否合法 | 🔴 嚴重 |
| Return Value | 回傳值是否可能為 null | 🟡 中度 |

### 掃描規則

```yaml
contract_rules:
  pre_conditions:
    - rule: null_check_for_objects
      description: "物件型別參數必須有 null 檢查"
      pattern: "public.*\\(.*[A-Z]\\w+\\s+\\w+"
      check: "Objects.requireNonNull|!= null"
      
    - rule: empty_check_for_collections
      description: "集合型別必須檢查是否為空"
      applies_to: ["List", "Set", "Collection"]
      check: "isEmpty()|!.*\\.isEmpty()"
      
    - rule: positive_check_for_quantities
      description: "數量類型必須檢查大於零"
      applies_to: ["quantity", "amount", "count", "size"]
      check: "> 0|>= 1|isPositive"

  post_conditions:
    - rule: non_null_return
      description: "標註 @NonNull 的回傳值必須確保不為 null"
      
    - rule: state_consistency
      description: "狀態變更後 invariants 必須成立"

  invariants:
    - rule: aggregate_validity
      description: "Aggregate 必須定義 assertInvariants() 方法"
      applies_to: "Aggregate"
```

## 與測試的整合

### 契約驅動測試

```java
class CreateOrderUseCaseTest {
    
    // ===== Pre-condition 測試 =====
    
    @Test
    @DisplayName("當 input 為 null 時，應拋出 NullPointerException")
    void should_throw_when_input_is_null() {
        // Given
        CreateOrderUseCase useCase = createUseCase();
        
        // When & Then
        assertThrows(NullPointerException.class, () -> {
            useCase.execute(null);
        });
    }
    
    @Test
    @DisplayName("當 items 為空時，應拋出 IllegalArgumentException")
    void should_throw_when_items_is_empty() {
        // Given
        Input input = new Input(customerId, Collections.emptyList(), address);
        
        // When & Then
        assertThrows(IllegalArgumentException.class, () -> {
            useCase.execute(input);
        });
    }
    
    // ===== Post-condition 測試 =====
    
    @Test
    @DisplayName("成功建立訂單後，應回傳有效的 OrderId")
    void should_return_valid_orderId_on_success() {
        // Given
        Input input = createValidInput();
        
        // When
        Output output = useCase.execute(input);
        
        // Then - 驗證 post-conditions
        assertNotNull(output);
        assertNotNull(output.getOrderId());
        assertEquals(OrderStatus.CREATED, output.getStatus());
    }
    
    @Test
    @DisplayName("成功建立訂單後，應發布 OrderCreatedEvent")
    void should_publish_event_on_success() {
        // Given
        Input input = createValidInput();
        
        // When
        useCase.execute(input);
        
        // Then - 驗證 post-condition
        verify(eventPublisher).publish(any(OrderCreatedEvent.class));
    }
}
```

## 檢查清單

### 實作新方法時

- [ ] 是否定義並記錄 pre-conditions？
- [ ] 是否在程式碼中驗證 pre-conditions？
- [ ] 是否定義 post-conditions？
- [ ] 是否有對應的測試案例？

### 實作 Entity/Aggregate 時

- [ ] 是否定義 invariants？
- [ ] 是否實作 assertInvariants() 方法？
- [ ] 建構子是否建立有效狀態？
- [ ] 所有公開方法是否維護 invariants？

### 代碼審查時

- [ ] pre-conditions 是否足夠嚴謹？
- [ ] 是否遺漏邊界條件？
- [ ] 錯誤訊息是否足夠清楚？
- [ ] 測試是否涵蓋所有契約？

## AI 幻覺預防

透過契約式設計，可以有效減少 AI 幻覺：

1. **明確邊界**：AI 必須先定義什麼是有效輸入
2. **強制思考**：AI 必須考慮異常情況
3. **可驗證性**：契約可以被測試驗證
4. **自我約束**：AI 生成的代碼有明確的行為規範

```
契約完整度 ∝ 1 / AI 幻覺發生率
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
