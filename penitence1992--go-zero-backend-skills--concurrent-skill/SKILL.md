---
name: concurrent-skill
description: This skill should be used when the user asks to "handle high concurrency", "implement concurrent processing", "use errgroup", "goroutine safe", "thread-safe queue", "高并发", "并发处理", "errgroup", or needs to handle high concurrency scenarios including goroutine safety wrappers, memory queues (fixed/dynamic), queue group management, and errgroup concurrent operations. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 高并发处理模式

## goroutine 安全包装

```go
import "github.com/zeromicro/go-zero/core/threading"

threading.GoSafe(func() {
    // 并发任务，panic 会自动恢复
})
```

## 固定大小任务队列

```go
type TaskQueue struct {
    mutex    sync.Mutex
    ch       chan func()  // 带缓冲区的 channel
    stop     chan struct{}
    closed   bool
    lastTime atomic.Int64
}

func NewTaskQueue(params ...int) *TaskQueue {
    var (
        workerSize = runtime.NumCPU()*2 + 2  // worker 数量
        bufSize    = 2000                     // 缓冲区大小
    )
    if len(params) > 0 {
        bufSize = params[0]
    }

    queue := &TaskQueue{
        ch:     make(chan func(), bufSize),
        stop:   make(chan struct{}),
        closed: false,
    }

    // 启动 workers
    for i := 0; i < workerSize; i++ {
        go func() {
            for {
                select {
                case fn, ok := <-queue.ch:
                    if !ok {
                        return
                    }
                    fn()  // panic 会自动恢复
                case <-queue.stop:
                    return
                }
            }
        }()
    }
    return queue
}

// 推送任务
func (q *TaskQueue) Push(fn func()) error {
    select {
    case <-q.stop:
        return TaskQueueClosedErr
    case q.ch <- fn:
        q.lastTime.Store(time.Now().Unix())
    }
    return nil
}

// 队列大小
func (q *TaskQueue) Size() int {
    return len(q.ch)
}
```

## 动态扩缩容队列

```go
type TaskDynamicQueue struct {
    ch          chan func()
    workers     []*worker
    initialSize int
    DynamicScale = 4  // 扩缩容系数
}

// 当队列长度 > worker 数量时，自动扩容 1.25 倍
// 连续 5 次检测到负载低时，自动缩容
```

## 队列组管理

```go
type QueueGroup struct {
    mutex   sync.RWMutex
    queues  map[string]*TaskQueue
}

func NewQueueGroup() *QueueGroup {
    return &QueueGroup{
        queues: make(map[string]*TaskQueue),
    }
}

func (g *QueueGroup) Get(key string) *TaskQueue {
    g.mutex.RLock()
    defer g.mutex.RUnlock()
    return g.queues[key]
}

func (g *QueueGroup) Set(key string, q *TaskQueue) {
    g.mutex.Lock()
    defer g.mutex.Unlock()
    g.queues[key] = q
}
```

## errgroup 并发查询

```go
import "golang.org/x/sync/errgroup"

func QueryMultipleTables(ctx context.Context, rDb *gorm.DB, tables []string) ([]Result, error) {
    var (
        eg, egCtx = errgroup.WithContext(ctx)
        mu        sync.Mutex
        results   []Result
    )

    for _, table := range tables {
        eg.Go(func() error {
            var data []Result
            err := rDb.WithContext(egCtx).
                Table(table).
                Where("status = ?", 1).
                Find(&data).Error

            if err != nil {
                return err
            }

            mu.Lock()
            results = append(results, data...)
            mu.Unlock()
            return nil
        })
    }

    if err := eg.Wait(); err != nil {
        return nil, err
    }
    return results, nil
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
