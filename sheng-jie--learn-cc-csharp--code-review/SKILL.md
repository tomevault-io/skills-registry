---
name: code-review
description: 代码审查方法论。用于系统化审查代码质量、安全性和最佳实践。 Use when this capability is needed.
metadata:
  author: sheng-jie
---

# 代码审查技能

本技能提供系统化的代码审查方法论和检查清单。

## 审查流程

### 1. 快速浏览（2分钟）

先获得整体印象：

```bash
# 查看文件结构
ls -la

# 统计代码行数
wc -l *.cs

# 查看最近修改
git log --oneline -10
```

### 2. 逐项检查（核心）

按以下顺序检查：

1. **安全问题** ← 最高优先级
2. **正确性问题**
3. **性能问题**
4. **可维护性问题**
5. **风格问题** ← 最低优先级

## 安全检查清单

### 🔴 严重

- [ ] **SQL 注入**: 是否使用参数化查询？
  ```csharp
  // ❌ 危险
  $"SELECT * FROM Users WHERE Id = {userId}"
  
  // ✅ 安全
  "SELECT * FROM Users WHERE Id = @Id", new { Id = userId }
  ```

- [ ] **密码处理**: 是否安全存储和比较？
  ```csharp
  // ❌ 危险
  if (inputPassword == storedPassword)
  
  // ✅ 安全（常量时间比较）
  if (CryptographicOperations.FixedTimeEquals(hash1, hash2))
  ```

- [ ] **敏感数据**: 日志中是否暴露密码/token？
  ```csharp
  // ❌ 危险
  _logger.LogInfo($"User {user} logged in with password {password}");
  
  // ✅ 安全
  _logger.LogInfo($"User {user} logged in");
  ```

### 🟠 重要

- [ ] **输入验证**: 所有外部输入是否验证？
- [ ] **权限检查**: API 端点是否有授权？
- [ ] **CORS 配置**: 是否过于宽松？
- [ ] **依赖安全**: 是否有已知漏洞？

```bash
# 检查 NuGet 包漏洞
dotnet list package --vulnerable
```

## 正确性检查清单

### 逻辑错误

- [ ] **边界条件**: 空值、零、最大值是否处理？
- [ ] **异常处理**: catch 块是否吞掉错误？
  ```csharp
  // ❌ 吞掉错误
  catch (Exception) { }
  
  // ✅ 至少记录
  catch (Exception ex) { _logger.LogError(ex, "Operation failed"); throw; }
  ```

- [ ] **资源释放**: IDisposable 是否正确处理？
  ```csharp
  // ❌ 可能泄露
  var client = new HttpClient();
  
  // ✅ 正确
  using var client = new HttpClient();
  ```

### 并发问题

- [ ] **线程安全**: 共享状态是否有竞态条件？
- [ ] **死锁**: async 代码是否有 `.Result` 或 `.Wait()`？
  ```csharp
  // ❌ 可能死锁
  var result = GetDataAsync().Result;
  
  // ✅ 安全
  var result = await GetDataAsync();
  ```

## 性能检查清单

### 常见问题

- [ ] **N+1 查询**: 循环中是否有数据库调用？
  ```csharp
  // ❌ N+1
  foreach (var order in orders)
      order.Customer = await db.GetCustomer(order.CustomerId);
  
  // ✅ 批量加载
  var customerIds = orders.Select(o => o.CustomerId).Distinct();
  var customers = await db.GetCustomers(customerIds);
  ```

- [ ] **内存分配**: 是否在热路径上频繁分配？
- [ ] **字符串拼接**: 是否使用 StringBuilder？
- [ ] **LINQ 滥用**: 是否多次枚举 IEnumerable？

### 检测工具

```bash
# 使用 BenchmarkDotNet
dotnet add package BenchmarkDotNet

# 内存分析
dotnet-counters monitor --process-id <PID>
```

## 可维护性检查清单

### 代码组织

- [ ] **单一职责**: 类/方法是否只做一件事？
- [ ] **方法长度**: 是否超过 50 行？考虑拆分
- [ ] **嵌套深度**: 是否超过 3 层？考虑提前返回

### 命名

- [ ] **有意义**: 名字是否描述意图？
  ```csharp
  // ❌ 含糊
  var d = GetData();
  
  // ✅ 清晰
  var activeUsers = GetActiveUsers();
  ```

- [ ] **一致性**: 是否遵循项目约定？

### 注释

- [ ] **必要性**: 注释是否解释"为什么"而非"是什么"？
- [ ] **准确性**: 注释是否与代码一致？

## 审查报告模板

```markdown
## 代码审查报告

**文件**: src/Services/AuthService.cs
**审查者**: [你的名字]
**日期**: [日期]

### 🔴 严重问题

1. **第 45 行 - SQL 注入风险**
   ```csharp
   // 当前
   var query = $"SELECT * FROM Users WHERE Email = '{email}'";
   
   // 建议
   var query = "SELECT * FROM Users WHERE Email = @Email";
   ```

### 🟠 重要问题

1. **第 78 行 - 缺少输入验证**
   用户输入 `email` 未经验证直接使用。

### 🟡 建议

1. **第 120 行 - 方法过长**
   `ProcessOrder` 方法有 150 行，建议拆分为：
   - `ValidateOrder()`
   - `CalculateTotal()`
   - `SaveOrder()`

### ✅ 优点

- 良好的异常处理
- 代码结构清晰
```

## 自动化工具

### 静态分析

```bash
# 使用 .NET Analyzers（内置）
# 在 .csproj 中启用
<PropertyGroup>
    <EnableNETAnalyzers>true</EnableNETAnalyzers>
    <AnalysisLevel>latest-recommended</AnalysisLevel>
</PropertyGroup>

# 使用 SonarQube
dotnet sonarscanner begin /k:"project-key"
dotnet build
dotnet sonarscanner end
```

### 格式化

```bash
# 使用 dotnet format
dotnet format

# 检查但不修改
dotnet format --verify-no-changes
```

## 常见反模式

### 1. 上帝类 (God Class)

一个类做太多事情。拆分为多个单一职责的类。

### 2. 魔法数字

```csharp
// ❌
if (status == 3) { ... }

// ✅
if (status == OrderStatus.Completed) { ... }
```

### 3. 注释掉的代码

删除它。用版本控制恢复。

### 4. 过早优化

先让它正确工作，再考虑优化。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sheng-jie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
