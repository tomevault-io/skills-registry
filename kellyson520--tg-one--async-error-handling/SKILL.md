---
name: async-error-handling
description: Expert guidance on Python async/await error handling patterns, context managers, and FastAPI lifecycle management Use when this capability is needed.
metadata:
  author: kellyson520
---

# 🎯 Triggers (触发条件)

- 当编写或修复涉及 `async def`, `await`, `asyncio` 的代码时
- 当实现 `@asynccontextmanager` 装饰的异步上下文管理器时
- 当遇到 `RuntimeError: generator didn't stop after athrow()` 错误时
- 当实现 FastAPI 的 `lifespan` 事件处理器时
- 当处理 `asyncio.CancelledError` 或其他异步异常时
- 当需要确保异步资源正确清理时

# 🧠 Role & Context (角色设定)

你是一位 **Python 异步编程专家 (Async Programming Specialist)**。你深刻理解 Python 的事件循环机制、协程生命周期和异常传播规则。你知道异步代码中的每一个 `try-except-finally` 块都可能影响整个应用的稳定性，因此你对异常处理极为谨慎。

## 核心理念
> **异步异常必须正确传播 (Async Exceptions Must Propagate Correctly)**
> 
> 在异步编程中，某些异常（如 `CancelledError`）是控制流信号，而非真正的错误。
> 吞掉这些异常会导致资源泄漏、死锁或运行时错误。

# ✅ Standards & Rules (执行标准)

## 1. 异步上下文管理器异常处理矩阵

| 异常类型 | 处理策略 | 是否重抛 | 原因 |
|---------|---------|---------|------|
| `asyncio.CancelledError` | 记录日志 + 清理资源 | ✅ **必须** | 取消信号，必须传播给调用者 |
| `asyncio.TimeoutError` | 根据业务逻辑 | ⚠️ 视情况 | 可能需要重试或降级 |
| `Exception` (通用异常) | 记录详细日志 | ✅ **建议** | 除非有明确的降级策略 |
| `KeyboardInterrupt` | 立即清理 | ✅ **必须** | 用户中断信号 |
| `SystemExit` | 立即清理 | ✅ **必须** | 系统退出信号 |

## 2. 标准异步上下文管理器模板

### ✅ 正确模式 (Recommended Pattern)

```python
from contextlib import asynccontextmanager
import asyncio
import logging

logger = logging.getLogger(__name__)

@asynccontextmanager
async def managed_resource():
    """标准异步上下文管理器模板"""
    # 1. 初始化资源
    resource = await initialize_resource()
    logger.info("Resource initialized")
    
    # 2. 使用标志位追踪取消状态
    cancelled = False
    
    try:
        # 3. 将资源交给调用者
        yield resource
        
    except asyncio.CancelledError:
        # 4. 捕获取消信号，标记状态
        logger.warning("Resource usage cancelled")
        cancelled = True
        # ⚠️ 不要在这里 raise，等待 finally 执行完毕
        
    except Exception as e:
        # 5. 处理其他异常
        logger.error(f"Error during resource usage: {e}", exc_info=True)
        raise  # 立即重抛业务异常
        
    finally:
        # 6. 无论如何都执行清理
        logger.info("Cleaning up resource")
        await cleanup_resource(resource)
        
        # 7. 清理完成后，重新抛出取消异常
        if cancelled:
            raise asyncio.CancelledError()
```

### ❌ 错误模式 (Anti-Pattern)

```python
@asynccontextmanager
async def bad_managed_resource():
    resource = await initialize_resource()
    
    try:
        yield resource
    except asyncio.CancelledError:
        logger.warning("Cancelled")
        # ❌ 错误：吞掉异常，不重抛
        pass  
    finally:
        await cleanup_resource(resource)
    # ❌ 结果：生成器无法正确停止，导致 RuntimeError
```

## 3. FastAPI Lifespan 最佳实践

### ✅ 标准 FastAPI Lifespan 实现

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
import asyncio
import logging

logger = logging.getLogger(__name__)

@asynccontextmanager
async def lifespan(app: FastAPI):
    """FastAPI 应用生命周期管理"""
    # Startup: 初始化资源
    logger.info("🚀 Application starting up")
    
    try:
        # 初始化数据库连接池
        await init_db_pool()
        
        # 初始化缓存
        await init_cache()
        
        # 启动后台任务
        background_tasks = await start_background_workers()
        
        logger.info("✅ Application startup complete")
        
    except Exception as e:
        logger.error(f"❌ Startup failed: {e}", exc_info=True)
        raise  # 启动失败，阻止应用运行
    
    # 标志位：追踪取消状态
    cancelled = False
    
    try:
        # 应用运行期间
        yield
        
    except asyncio.CancelledError:
        # 应用被取消（如 Ctrl+C）
        logger.warning("⚠️ Application shutdown requested (Cancelled)")
        cancelled = True
        
    except Exception as e:
        # 运行时异常
        logger.error(f"❌ Runtime error: {e}", exc_info=True)
        raise
        
    finally:
        # Shutdown: 清理资源
        logger.info("🛑 Application shutting down")
        
        try:
            # 停止后台任务
            await stop_background_workers(background_tasks)
            
            # 关闭缓存
            await close_cache()
            
            # 关闭数据库连接池
            await close_db_pool()
            
            logger.info("✅ Application shutdown complete")
            
        except Exception as e:
            logger.error(f"⚠️ Error during shutdown: {e}", exc_info=True)
        
        # 清理完成后，重新抛出取消异常
        if cancelled:
            raise asyncio.CancelledError()

# 创建应用
app = FastAPI(lifespan=lifespan)
```

## 4. 异步异常处理决策树

```
遇到异步异常
    ├─ 是 CancelledError？
    │   ├─ Yes → 标记状态 → 执行 finally → 重抛
    │   └─ No → 继续判断
    │
    ├─ 是 TimeoutError？
    │   ├─ 可以重试？ → 重试逻辑
    │   └─ 不可重试？ → 记录日志 → 重抛或降级
    │
    ├─ 是业务异常？
    │   ├─ 可以恢复？ → 降级处理 → 返回默认值
    │   └─ 不可恢复？ → 记录日志 → 重抛
    │
    └─ 是系统异常 (KeyboardInterrupt/SystemExit)？
        └─ 立即清理 → 重抛
```

# 🚀 Workflow (工作流)

## 场景 1: 实现新的异步上下文管理器

1. **复制模板**: 使用本技能提供的标准模板
2. **填充逻辑**: 
   - 在 `yield` 前添加初始化代码
   - 在 `finally` 中添加清理代码
3. **异常处理**:
   - 添加 `cancelled = False` 标志位
   - 在 `except asyncio.CancelledError` 中设置 `cancelled = True`
   - 在 `finally` 末尾检查并重抛
4. **测试**: 编写单元测试，模拟取消场景

## 场景 2: 修复现有的异步上下文管理器错误

1. **定位问题**: 检查是否吞掉了 `CancelledError`
2. **应用模式**: 
   - 添加标志位
   - 移除 `except` 块中的 `pass` 或 `return`
   - 在 `finally` 末尾重抛
3. **验证**: 运行应用并测试关闭流程

## 场景 3: 实现 FastAPI Lifespan

1. **使用模板**: 复制本技能提供的 FastAPI lifespan 模板
2. **自定义资源**: 替换 `init_db_pool()` 等为实际的初始化逻辑
3. **测试关闭**: 
   - 启动应用
   - 发送 SIGTERM 或 Ctrl+C
   - 检查日志，确认清理逻辑执行

# 💡 Examples (少样本提示)

## Example 1: 数据库连接池管理

```python
@asynccontextmanager
async def db_connection_pool():
    """数据库连接池生命周期管理"""
    pool = await asyncpg.create_pool(
        dsn=settings.DATABASE_URL,
        min_size=5,
        max_size=20
    )
    logger.info(f"📊 DB Pool created: {pool.get_size()} connections")
    
    cancelled = False
    try:
        yield pool
    except asyncio.CancelledError:
        logger.warning("⚠️ DB Pool usage cancelled")
        cancelled = True
    except Exception as e:
        logger.error(f"❌ DB Pool error: {e}", exc_info=True)
        raise
    finally:
        logger.info("🛑 Closing DB Pool")
        await pool.close()
        logger.info("✅ DB Pool closed")
        
        if cancelled:
            raise asyncio.CancelledError()
```

## Example 2: 后台任务管理

```python
@asynccontextmanager
async def background_task_manager():
    """后台任务生命周期管理"""
    tasks = []
    
    # 启动多个后台任务
    tasks.append(asyncio.create_task(periodic_cleanup()))
    tasks.append(asyncio.create_task(metrics_collector()))
    logger.info(f"🔄 Started {len(tasks)} background tasks")
    
    cancelled = False
    try:
        yield tasks
    except asyncio.CancelledError:
        logger.warning("⚠️ Background tasks cancelled")
        cancelled = True
    finally:
        logger.info("🛑 Stopping background tasks")
        
        # 取消所有任务
        for task in tasks:
            if not task.done():
                task.cancel()
        
        # 等待所有任务完成（包括取消）
        await asyncio.gather(*tasks, return_exceptions=True)
        logger.info("✅ All background tasks stopped")
        
        if cancelled:
            raise asyncio.CancelledError()
```

# 📚 Reference (参考资料)

- [PEP 492 - Coroutines with async and await syntax](https://peps.python.org/pep-0492/)
- [Python asyncio - Task Cancellation](https://docs.python.org/3/library/asyncio-task.html#task-cancellation)
- [contextlib.asynccontextmanager](https://docs.python.org/3/library/contextlib.html#contextlib.asynccontextmanager)
- [FastAPI - Lifespan Events](https://fastapi.tiangolo.com/advanced/events/)

# 🔍 Common Pitfalls (常见陷阱)

1. **❌ 吞掉 CancelledError**
   ```python
   except asyncio.CancelledError:
       pass  # ❌ 错误！
   ```

2. **❌ 在 except 块中直接 raise**
   ```python
   except asyncio.CancelledError:
       raise  # ❌ 错误！finally 未执行
   ```

3. **❌ 忘记重抛**
   ```python
   finally:
       cleanup()
       # ❌ 忘记检查 cancelled 标志
   ```

4. **❌ 混淆 Exception 和 BaseException**
   ```python
   except Exception:  # ❌ 无法捕获 CancelledError (继承自 BaseException)
   ```

# ✅ Checklist (检查清单)

在提交涉及异步上下文管理器的代码前，确认：

- [ ] 是否使用了标志位追踪取消状态？
- [ ] `except asyncio.CancelledError` 块是否只标记状态，不重抛？
- [ ] `finally` 块是否在末尾检查标志位并重抛？
- [ ] 清理逻辑是否在 `finally` 中，确保一定执行？
- [ ] 是否添加了足够的日志，便于追踪生命周期？
- [ ] 是否编写了测试用例，模拟取消场景？

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kellyson520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
