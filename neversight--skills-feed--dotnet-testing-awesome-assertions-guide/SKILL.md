---
name: dotnet-testing-awesome-assertions-guide
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# AwesomeAssertions 流暢斷言指南

本技能提供使用 AwesomeAssertions 進行高品質測試斷言的完整指南，涵蓋基礎語法、進階技巧與最佳實踐。

## 關於 AwesomeAssertions

**AwesomeAssertions** 是 FluentAssertions 的社群分支版本，使用 **Apache 2.0** 授權，完全免費且無商業使用限制。

### 核心特色

- ✅ **完全免費**：Apache 2.0 授權，適合商業專案使用
- 🔗 **流暢語法**：支援方法鏈結的自然語言風格
- 📦 **豐富斷言**：涵蓋物件、集合、字串、數值、例外等各種類型
- 💬 **優秀錯誤訊息**：提供詳細且易理解的失敗資訊
- ⚡ **高性能**：優化的實作確保測試執行效率
- 🔧 **可擴展**：支援自訂 Assertions 方法

### 與 FluentAssertions 的關係

AwesomeAssertions 是 FluentAssertions 的社群 fork，主要差異：

| 項目           | FluentAssertions   | AwesomeAssertions      |
| -------------- | ------------------ | ---------------------- |
| **授權**       | 商業專案需付費     | Apache 2.0（完全免費） |
| **命名空間**   | `FluentAssertions` | `AwesomeAssertions`    |
| **API 相容性** | 原版               | 高度相容               |
| **社群支援**   | 官方維護           | 社群維護               |

---

## 安裝與設定

### NuGet 套件安裝

```bash
# .NET CLI
dotnet add package AwesomeAssertions

# Package Manager Console
Install-Package AwesomeAssertions
```

### csproj 設定（推薦）

```xml
<ItemGroup>
  <PackageReference Include="AwesomeAssertions" Version="9.1.0" PrivateAssets="all" />
</ItemGroup>
```

### 命名空間引用

```csharp
using AwesomeAssertions;
using Xunit;
```

---

## 核心 Assertions 語法

### 1. 物件斷言（Object Assertions）

#### 基本檢查

```csharp
[Fact]
public void Object_基本斷言_應正常運作()
{
    var user = new User { Id = 1, Name = "John", Email = "john@example.com" };
    
    // 空值檢查
    user.Should().NotBeNull();
    
    // 類型檢查
    user.Should().BeOfType<User>();
    user.Should().BeAssignableTo<IUser>();
    
    // 相等性檢查
    var anotherUser = new User { Id = 1, Name = "John", Email = "john@example.com" };
    user.Should().BeEquivalentTo(anotherUser);
}
```

#### 屬性驗證

```csharp
[Fact]
public void Object_屬性驗證_應正常運作()
{
    var user = new User { Id = 1, Name = "John", Email = "john@example.com" };
    
    // 單一屬性驗證
    user.Id.Should().Be(1);
    user.Name.Should().Be("John");
    user.Email.Should().Contain("@");
    
    // 多屬性驗證
    user.Should().BeEquivalentTo(new 
    { 
        Id = 1, 
        Name = "John" 
    });
}
```

### 2. 字串斷言（String Assertions）

#### 內容驗證

```csharp
[Fact]
public void String_內容驗證_應正常運作()
{
    var text = "Hello World";
    
    // 基本檢查
    text.Should().NotBeNullOrEmpty();
    text.Should().NotBeNullOrWhiteSpace();
    
    // 內容檢查
    text.Should().Contain("Hello");
    text.Should().StartWith("Hello");
    text.Should().EndWith("World");
    
    // 精確匹配
    text.Should().Be("Hello World");
    text.Should().BeEquivalentTo("hello world"); // 忽略大小寫
}
```

#### 模式匹配

```csharp
[Fact]
public void String_模式匹配_應正常運作()
{
    var email = "user@example.com";
    
    // 正規表示式匹配
    email.Should().MatchRegex(@"^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$");
    
    // 長度驗證
    email.Should().HaveLength(16);
    email.Should().HaveLengthGreaterThan(10);
    email.Should().HaveLengthLessThanOrEqualTo(50);
}
```

### 3. 數值斷言（Numeric Assertions）

#### 範圍與比較

```csharp
[Fact]
public void Numeric_範圍檢查_應正常運作()
{
    var value = 10;
    
    // 比較運算
    value.Should().BeGreaterThan(5);
    value.Should().BeLessThan(15);
    value.Should().BeGreaterThanOrEqualTo(10);
    value.Should().BeLessThanOrEqualTo(10);
    
    // 範圍檢查
    value.Should().BeInRange(5, 15);
    value.Should().BeOneOf(8, 9, 10, 11);
}
```

#### 浮點數處理

```csharp
[Fact]
public void Numeric_浮點數精度_應正常運作()
{
    var pi = 3.14159;
    
    // 精度比較
    pi.Should().BeApproximately(3.14, 0.01);
    
    // 特殊值檢查
    double.NaN.Should().Be(double.NaN);
    double.PositiveInfinity.Should().BePositiveInfinity();
    
    // 符號檢查
    pi.Should().BePositive();
    (-5.5).Should().BeNegative();
}
```

### 4. 集合斷言（Collection Assertions）

#### 基本檢查

```csharp
[Fact]
public void Collection_基本驗證_應正常運作()
{
    var numbers = new[] { 1, 2, 3, 4, 5 };
    
    // 數量檢查
    numbers.Should().NotBeEmpty();
    numbers.Should().HaveCount(5);
    numbers.Should().HaveCountGreaterThan(3);
    
    // 內容檢查
    numbers.Should().Contain(3);
    numbers.Should().ContainSingle(x => x == 3);
    numbers.Should().NotContain(0);
    
    // 完整比對
    numbers.Should().Equal(1, 2, 3, 4, 5);
    numbers.Should().BeEquivalentTo(new[] { 5, 4, 3, 2, 1 }); // 忽略順序
}
```

#### 順序與唯一性

```csharp
[Fact]
public void Collection_順序驗證_應正常運作()
{
    var numbers = new[] { 1, 2, 3, 4, 5 };
    
    // 順序檢查
    numbers.Should().BeInAscendingOrder();
    numbers.Should().BeInDescendingOrder();
    
    // 唯一性檢查
    numbers.Should().OnlyHaveUniqueItems();
    
    // 子集檢查
    numbers.Should().BeSubsetOf(new[] { 1, 2, 3, 4, 5, 6, 7 });
    numbers.Should().Contain(x => x > 3);
}
```

#### 複雜物件集合

```csharp
[Fact]
public void Collection_複雜物件_應正常運作()
{
    var users = new[]
    {
        new User { Id = 1, Name = "John", Age = 30 },
        new User { Id = 2, Name = "Jane", Age = 25 },
        new User { Id = 3, Name = "Bob", Age = 35 }
    };
    
    // 條件過濾
    users.Should().Contain(u => u.Name == "John");
    users.Should().OnlyContain(u => u.Age >= 18);
    
    // 全部滿足
    users.Should().AllSatisfy(u => 
    {
        u.Id.Should().BeGreaterThan(0);
        u.Name.Should().NotBeNullOrEmpty();
    });
    
    // LINQ 整合
    users.Where(u => u.Age > 30).Should().HaveCount(1);
}
```

### 5. 例外斷言（Exception Assertions）

#### 基本例外處理

```csharp
[Fact]
public void Exception_基本驗證_應正常運作()
{
    var service = new UserService();
    
    // 預期拋出例外
    Action act = () => service.GetUser(-1);
    
    act.Should().Throw<ArgumentException>()
       .WithMessage("*User ID*")
       .And.ParamName.Should().Be("userId");
}
```

#### 不應拋出例外

```csharp
[Fact]
public void Exception_不應拋出_應正常運作()
{
    var calculator = new Calculator();
    
    // 不應拋出任何例外
    Action act = () => calculator.Add(1, 2);
    act.Should().NotThrow();
    
    // 不應拋出特定例外
    act.Should().NotThrow<DivideByZeroException>();
}
```

#### 巢狀例外

```csharp
[Fact]
public void Exception_巢狀例外_應正常運作()
{
    var service = new DatabaseService();
    
    Action act = () => service.Connect("invalid");
    
    act.Should().Throw<DatabaseConnectionException>()
       .WithInnerException<ArgumentException>()
       .WithMessage("*connection string*");
}
```

### 6. 非同步斷言（Async Assertions）

#### Task 完成驗證

```csharp
[Fact]
public async Task Async_任務完成_應正常運作()
{
    var service = new UserService();
    
    // 等待任務完成
    var task = service.GetUserAsync(1);
    await task.Should().CompleteWithinAsync(TimeSpan.FromSeconds(5));
    
    // 驗證結果
    task.Result.Should().NotBeNull();
    task.Result.Id.Should().Be(1);
}
```

#### 非同步例外

```csharp
[Fact]
public async Task Async_例外處理_應正常運作()
{
    var service = new ApiService();
    
    Func<Task> act = async () => await service.CallInvalidEndpointAsync();
    
    await act.Should().ThrowAsync<HttpRequestException>()
             .WithMessage("*404*");
}
```

---

## 進階技巧：複雜物件比對

### 深度物件比較

#### 完整物件比對

```csharp
[Fact]
public void ComplexObject_深度比較_應正常運作()
{
    var expected = new Order
    {
        Id = 1,
        CustomerName = "John Doe",
        Items = new[]
        {
            new OrderItem { ProductId = 1, Quantity = 2, Price = 10.5m },
            new OrderItem { ProductId = 2, Quantity = 1, Price = 25.0m }
        },
        TotalAmount = 46.0m,
        CreatedAt = DateTime.Now
    };
    
    var actual = orderService.CreateOrder(orderRequest);
    
    // 深度物件比較
    actual.Should().BeEquivalentTo(expected);
}
```

#### 排除特定屬性

```csharp
[Fact]
public void ComplexObject_排除屬性_應正常運作()
{
    var user = userService.CreateUser("john@example.com");
    
    user.Should().BeEquivalentTo(new
    {
        Email = "john@example.com",
        IsActive = true
    }, options => options
        .Excluding(u => u.Id)           // 排除自動生成的 ID
        .Excluding(u => u.CreatedAt)    // 排除時間戳記
        .Excluding(u => u.UpdatedAt)
    );
}
```

#### 動態欄位排除

```csharp
[Fact]
public void ComplexObject_動態排除_應正常運作()
{
    var entity = entityService.CreateEntity(data);
    
    // 使用模式排除所有時間相關欄位
    entity.Should().BeEquivalentTo(expectedEntity, options => options
        .Excluding(ctx => ctx.Path.EndsWith("At"))
        .Excluding(ctx => ctx.Path.EndsWith("Time"))
        .Excluding(ctx => ctx.Path.Contains("Timestamp"))
    );
}
```

### 循環參考處理

```csharp
[Fact]
public void ComplexObject_循環參考_應正常運作()
{
    var parent = new TreeNode { Value = "Root" };
    var child = new TreeNode { Value = "Child", Parent = parent };
    parent.Children = new[] { child };
    
    var actualTree = treeService.GetTree("Root");
    
    // 處理循環參考
    actualTree.Should().BeEquivalentTo(parent, options => options
        .IgnoringCyclicReferences()
        .WithMaxRecursionDepth(10)
    );
}
```

---

## 進階技巧：自訂 Assertions 擴展

### 領域特定 Assertions

建立專案特定的斷言方法，提升測試可讀性與可維護性。

#### 範例：電商領域 Assertions

參考 [templates/custom-assertions-template.cs](templates/custom-assertions-template.cs) 瞭解完整實作。

```csharp
public static class ECommerceAssertions
{
    public static AndConstraint<ObjectAssertions> BeValidProduct(
        this ObjectAssertions assertions)
    {
        var product = assertions.Subject as Product;
        
        product.Should().NotBeNull();
        product!.Id.Should().BeGreaterThan(0);
        product.Name.Should().NotBeNullOrEmpty();
        product.Price.Should().BeGreaterThan(0);
        
        return new AndConstraint<ObjectAssertions>(assertions);
    }
    
    public static AndConstraint<ObjectAssertions> BeValidOrder(
        this ObjectAssertions assertions)
    {
        var order = assertions.Subject as Order;
        
        order.Should().NotBeNull();
        order!.Items.Should().NotBeNullOrEmpty();
        order.TotalAmount.Should().BeGreaterThan(0);
        
        return new AndConstraint<ObjectAssertions>(assertions);
    }
}
```

#### 使用自訂 Assertions

```csharp
[Fact]
public void Product_建立產品_應為有效產品()
{
    var product = productService.Create("Laptop", 999.99m);
    
    // 使用領域特定斷言
    product.Should().BeValidProduct();
    product.Name.Should().Be("Laptop");
}
```

### 可重用排除擴展

```csharp
public static class SmartExclusionExtensions
{
    public static EquivalencyOptions<T> ExcludingAutoGeneratedFields<T>(
        this EquivalencyOptions<T> options)
    {
        return options
            .Excluding(ctx => ctx.Path.EndsWith("Id") && 
                            ctx.SelectedMemberInfo.Name.StartsWith("Generated"))
            .Excluding(ctx => ctx.Path.EndsWith("At"))
            .Excluding(ctx => ctx.Path.Contains("Version"))
            .Excluding(ctx => ctx.Path.Contains("Timestamp"));
    }
    
    public static EquivalencyOptions<T> ExcludingAuditFields<T>(
        this EquivalencyOptions<T> options)
    {
        return options
            .Excluding(ctx => ctx.Path.Contains("CreatedBy"))
            .Excluding(ctx => ctx.Path.Contains("CreatedAt"))
            .Excluding(ctx => ctx.Path.Contains("ModifiedBy"))
            .Excluding(ctx => ctx.Path.Contains("ModifiedAt"));
    }
}
```

使用範例：

```csharp
[Fact]
public void Entity_比對_應使用智慧排除()
{
    var user = userService.CreateUser("test@example.com");
    var retrieved = userService.GetUser(user.Id);
    
    retrieved.Should().BeEquivalentTo(user, options => options
        .ExcludingAutoGeneratedFields()
        .ExcludingAuditFields()
    );
}
```

---

## 效能最佳化策略

### 大量資料斷言

處理大量資料時的最佳實踐：

```csharp
[Fact]
public void LargeCollection_效能優化_應快速執行()
{
    var largeDataset = Enumerable.Range(1, 100000)
        .Select(i => new DataRecord { Id = i, Value = $"Record_{i}" })
        .ToList();
    
    var processed = dataProcessor.ProcessLargeDataset(largeDataset);
    
    // 快速數量檢查
    processed.Should().HaveCount(largeDataset.Count);
    
    // 抽樣驗證（避免全量比對）
    var sampleSize = Math.Min(1000, processed.Count / 10);
    var sampleIndices = Enumerable.Range(0, sampleSize)
        .Select(i => Random.Shared.Next(processed.Count))
        .Distinct()
        .ToList();
    
    foreach (var index in sampleIndices)
    {
        processed[index].Should().NotBeNull();
        processed[index].Id.Should().BeGreaterThan(0);
    }
}
```

### 選擇性屬性比對

```csharp
[Fact]
public void ComplexObject_選擇性比對_應提升效能()
{
    var order = orderService.CreateOrder(request);
    
    // 只比對關鍵屬性，而非全物件掃描
    order.Should().BeEquivalentTo(new
    {
        CustomerId = 123,
        TotalAmount = 999.99m,
        Status = "Pending"
    }, options => options
        .ExcludingMissingMembers()
    );
}
```

---

## 最佳實踐與團隊標準

### 測試命名規範

遵循 `方法_情境_預期結果` 模式：

```csharp
public class UserServiceTests
{
    [Fact]
    public void CreateUser_有效電子郵件_應回傳啟用的使用者()
    {
        // Arrange
        var email = "john@example.com";
        
        // Act
        var user = userService.CreateUser(email);
        
        // Assert
        user.Should().NotBeNull();
        user.Email.Should().Be(email);
        user.IsActive.Should().BeTrue();
    }
    
    [Theory]
    [InlineData("", "Email cannot be empty")]
    [InlineData(null, "Email cannot be null")]
    public void CreateUser_無效電子郵件_應拋出參數例外(
        string invalidEmail, 
        string expectedMessage)
    {
        Action act = () => userService.CreateUser(invalidEmail);
        
        act.Should().Throw<ArgumentException>()
           .WithMessage($"*{expectedMessage}*");
    }
}
```

### 錯誤訊息優化

提供清晰的失敗上下文：

```csharp
[Fact]
public void Payment_無效金額_應提供詳細錯誤()
{
    var payment = new PaymentRequest { Amount = -100 };
    
    var result = paymentService.ProcessPayment(payment);
    
    // 提供詳細的失敗原因
    result.IsSuccess.Should().BeFalse(
        "because negative payment amounts are not allowed");
    
    result.ErrorMessage.Should().Contain("amount", 
        "because error message should specify the problematic field");
    
    result.ErrorCode.Should().Be("INVALID_AMOUNT",
        "because specific error codes help with troubleshooting");
}
```

### AssertionScope 使用

收集多個失敗訊息：

```csharp
[Fact]
public void User_完整驗證_應收集所有失敗()
{
    var user = userService.CreateUser(testData);
    
    using (new AssertionScope())
    {
        user.Should().NotBeNull("User creation should not fail");
        user.Id.Should().BeGreaterThan(0, "User should have valid ID");
        user.Email.Should().NotBeNullOrEmpty("Email is required");
        user.IsActive.Should().BeTrue("New users should be active");
    }
    // 所有失敗的斷言會一次顯示
}
```

---

## 常見情境與解決方案

### 情境 1：API 回應驗證

```csharp
[Fact]
public void API_使用者資料_應符合規格()
{
    var response = apiClient.GetUserProfile(userId);
    
    response.StatusCode.Should().Be(200);
    response.Content.Should().NotBeNullOrEmpty();
    
    var user = JsonSerializer.Deserialize<User>(response.Content);
    
    user.Should().BeEquivalentTo(new
    {
        Id = userId,
        Email = expectedEmail
    }, options => options
        .Including(u => u.Id)
        .Including(u => u.Email)
    );
}
```

### 情境 2：資料庫實體驗證

```csharp
[Fact]
public void Database_儲存實體_應正確持久化()
{
    var user = new User 
    { 
        Name = "John", 
        Email = "john@example.com" 
    };
    
    dbContext.Users.Add(user);
    dbContext.SaveChanges();
    
    var saved = dbContext.Users.Find(user.Id);
    
    saved.Should().BeEquivalentTo(user, options => options
        .Excluding(u => u.CreatedAt)
        .Excluding(u => u.UpdatedAt)
        .Excluding(u => u.RowVersion)
    );
}
```

### 情境 3：事件驗證

```csharp
[Fact]
public void Event_發佈事件_應包含正確資料()
{
    var eventRaised = false;
    OrderCreatedEvent? capturedEvent = null;
    
    eventBus.Subscribe<OrderCreatedEvent>(e => 
    {
        eventRaised = true;
        capturedEvent = e;
    });
    
    orderService.CreateOrder(orderRequest);
    
    eventRaised.Should().BeTrue("Order creation should raise event");
    capturedEvent.Should().NotBeNull();
    capturedEvent!.OrderId.Should().BeGreaterThan(0);
    capturedEvent.TotalAmount.Should().Be(expectedAmount);
}
```

---

## 疑難排解

### 問題 1：BeEquivalentTo 失敗但物件看起來相同

**原因**：可能包含自動生成欄位或時間戳記

**解決方案**：

```csharp
// 排除動態欄位
actual.Should().BeEquivalentTo(expected, options => options
    .Excluding(x => x.Id)
    .Excluding(x => x.CreatedAt)
    .Excluding(x => x.UpdatedAt)
);
```

### 問題 2：集合順序不同導致失敗

**原因**：集合順序不同

**解決方案**：

```csharp
// 使用 BeEquivalentTo 忽略順序
actual.Should().BeEquivalentTo(expected); // 不檢查順序

// 或明確指定需要檢查順序
actual.Should().Equal(expected); // 檢查順序
```

### 問題 3：浮點數比較失敗

**原因**：浮點數精度問題

**解決方案**：

```csharp
// 使用精度容差
actualValue.Should().BeApproximately(expectedValue, 0.001);
```

---

## 何時使用此技能

### 適用情境

✅ 撰寫單元測試或整合測試時
✅ 需要驗證複雜物件結構時
✅ 比對 API 回應或資料庫實體時
✅ 需要清晰的失敗訊息時
✅ 建立領域特定測試標準時

### 不適用情境

❌ 效能測試（使用專用 benchmarking 工具）
❌ 負載測試（使用 K6、JMeter 等）
❌ UI 測試（使用 Playwright、Selenium）

---

## 與其他技能的配合

### 與 unit-test-fundamentals 搭配

先使用 `unit-test-fundamentals` 建立測試結構，再使用本技能撰寫斷言：

```csharp
[Fact]
public void Calculator_Add_兩個正數_應回傳總和()
{
    // Arrange - 遵循 3A Pattern
    var calculator = new Calculator();
    
    // Act
    var result = calculator.Add(2, 3);
    
    // Assert - 使用 AwesomeAssertions
    result.Should().Be(5);
}
```

### 與 test-naming-conventions 搭配

使用 `test-naming-conventions` 的命名規範，搭配本技能的斷言：

```csharp
[Fact]
public void CreateUser_有效資料_應回傳啟用使用者()
{
    var user = userService.CreateUser("test@example.com");
    
    user.Should().NotBeNull()
        .And.BeOfType<User>();
    user.IsActive.Should().BeTrue();
}
```

### 與 xunit-project-setup 搭配

在 `xunit-project-setup` 建立的專案中安裝並使用 AwesomeAssertions。

---

## 參考資源

### 原始文章

本技能內容提煉自「老派軟體工程師的測試修練 - 30 天挑戰」系列文章：

- **Day 04 - AwesomeAssertions 基礎應用與實戰技巧**
  - 鐵人賽文章：https://ithelp.ithome.com.tw/articles/10374188
  - 範例程式碼：https://github.com/kevintsengtw/30Days_in_Testing_Samples/tree/main/day04

- **Day 05 - AwesomeAssertions 進階技巧與複雜情境應用**
  - 鐵人賽文章：https://ithelp.ithome.com.tw/articles/10374425
  - 範例程式碼：https://github.com/kevintsengtw/30Days_in_Testing_Samples/tree/main/day05

### 官方資源

- **AwesomeAssertions GitHub**：https://github.com/AwesomeAssertions/AwesomeAssertions
- **AwesomeAssertions 官方文件**：https://awesomeassertions.org/

### 相關文章

- **Fluent Assertions 授權變化討論**：https://www.dotblogs.com.tw/mrkt/2025/04/19/152408

---

## 總結

AwesomeAssertions 提供了強大且可讀的斷言語法，是撰寫高品質測試的重要工具。透過：

1. **流暢語法**：讓測試程式碼更易讀
2. **豐富斷言**：涵蓋各種資料類型
3. **自訂擴展**：建立領域特定斷言
4. **效能優化**：處理大量資料情境
5. **完全免費**：Apache 2.0 授權無商業限制

記住：好的斷言不僅能驗證結果，更能清楚表達預期行為，並在失敗時提供有用的診斷資訊。

參考 [templates/assertion-examples.cs](templates/assertion-examples.cs) 查看更多實用範例。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
