---
name: distributed-lock-skill
description: This skill should be used when the user asks to "implement distributed lock", "Redis lock", "mutex lock", "prevent concurrent conflict", "分布式锁", "Redis互斥锁", or needs to implement distributed lock using Redis with Lua scripts for atomic operations including lock acquisition, release, renewal, and preventing concurrent conflicts. Use when this capability is needed.
metadata:
  author: penitence1992
---

# Redis 分布式锁

## 锁实现

```go
type RedisLock struct {
    store   red.UniversalClient
    seconds uint32
    key     string
    id      string  // 锁的唯一标识
}

func NewRedisLock(store red.UniversalClient, key string) *RedisLock {
    return &RedisLock{
        store: store,
        key:   key,
        id:    stringx.Randn(16),  // 随机生成 16 位唯一标识
    }
}
```

## Lua 脚本保证原子性

```go
const (
    // 锁获取脚本 - NX + PX 模式
    lockLuaScript = `if redis.call("GET", KEYS[1]) == ARGV[1] then
        redis.call("SET", KEYS[1], ARGV[1], "PX", ARGV[2])
        return "OK"
    else
        return redis.call("SET", KEYS[1], ARGV[1], "NX", "PX", ARGV[2])
    end`

    // 锁释放脚本 - 防止误删其他锁
    delLuaScript = `if redis.call("GET", KEYS[1]) == ARGV[1] then
        return redis.call("DEL", KEYS[1])
    else
        return 0
    end`
)

var (
    lockScript = red.NewScript(lockLuaScript)
    delScript  = red.NewScript(delLuaScript)
)
```

## 获取锁

```go
// 简单获取
func (rl *RedisLock) Acquire() (bool, error) {
    return rl.AcquireCtx(context.Background())
}

func (rl *RedisLock) AcquireCtx(ctx context.Context) (bool, error) {
    seconds := atomic.LoadUint32(&rl.seconds)
    resp, err := lockScript.Run(ctx, rl.store, []string{rl.key}, []string{
        rl.id, strconv.Itoa(int(seconds)*1000 + 500),  // 毫秒 + 容差
    }).Result()

    if err != nil {
        return false, err
    }
    return resp == "OK", nil
}

// 尝试获取锁（带超时）
func (rl *RedisLock) TryAcquireCtx(ctx context.Context, duration time.Duration) (bool, error) {
    start := time.Now().UnixMilli()
    for {
        val, err := rl.AcquireCtx(ctx)
        if err != nil {
            return false, err
        }
        if val {
            return true, nil
        }
        if time.Now().UnixMilli() >= start+duration.Milliseconds() {
            break
        }
        time.Sleep(time.Millisecond * 150)
    }
    return false, nil
}

// 设置过期时间
func (rl *RedisLock) SetExpire(seconds int) {
    atomic.StoreUint32(&rl.seconds, uint32(seconds))
}
```

## 释放锁

```go
func (rl *RedisLock) Release() (bool, error) {
    return rl.ReleaseCtx(context.Background())
}

func (rl *RedisLock) ReleaseCtx(ctx context.Context) (bool, error) {
    resp, err := delScript.Run(ctx, rl.store, []string{rl.key}, []string{rl.id}).Result()
    if err != nil {
        return false, err
    }
    return resp == 1, nil  // 只有删除成功才返回 true
}
```

## 使用示例

```go
lock := lock.NewRedisLock(rds, "my-lock-key")
lock.SetExpire(30)  // 30 秒过期

// 获取锁
acquired, err := lock.TryAcquireCtx(ctx, time.Second*10)
if err != nil {
    return err
}
if !acquired {
    return errors.New("failed to acquire lock")
}
defer lock.Release()

// 业务逻辑
doSomething()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
