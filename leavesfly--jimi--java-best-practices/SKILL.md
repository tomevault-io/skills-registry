---
name: java-best-practices
description: Java 编码最佳实践与设计模式 Use when this capability is needed.
metadata:
  author: leavesfly
---

# Java 最佳实践技能包

## 编码规范

### 命名规范
- **类名**：PascalCase（UserService）
- **方法/变量**：camelCase（getUserById）
- **常量**：UPPER_SNAKE_CASE（MAX_SIZE）
- **包名**：小写（com.example.service）

### 常用设计模式

**单例模式（枚举实现）**：
```java
public enum Singleton {
    INSTANCE;
    public void doSomething() {}
}
```

**工厂模式**：
```java
public class UserFactory {
    public static User createUser(String type) {
        return switch (type) {
            case "admin" -> new AdminUser();
            case "guest" -> new GuestUser();
            default -> new RegularUser();
        };
    }
}
```

**Builder 模式**：
```java
User user = User.builder()
    .name("张三")
    .age(25)
    .build();
```

## Stream API

```java
List<String> names = users.stream()
    .filter(u -> u.getAge() > 18)
    .map(User::getName)
    .collect(Collectors.toList());
```

## 异常处理

```java
try {
    // 业务逻辑
} catch (SpecificException e) {
    log.error("Error: {}", e.getMessage(), e);
    throw new BusinessException("操作失败");
} finally {
    // 清理资源
}
```

## 并发编程

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(() -> {
    // 异步任务
});
```

## Optional 使用

```java
Optional<User> user = userRepository.findById(id);
return user.orElseThrow(() -> new NotFoundException());
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leavesfly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
