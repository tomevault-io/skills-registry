---
name: go-zero-service-pattern-skill
description: This skill should be used when the user asks to "implement go-zero service layer", "create service pattern", "ServiceContext pattern", "cache query pattern", "transaction handling", "errgroup concurrent initialization", "go-zero服务层代码模式", "服务层开发", or needs to develop go-zero service layer code including ServiceContext global context, service layer specifications, cache query patterns, transaction processing, and errgroup concurrent initialization. Use when this capability is needed.
metadata:
  author: penitence1992
---

# go-zero 服务层代码模式

## ServiceContext 全局上下文

```go
package csvc

var ContextSvc *ServiceContext

type ServiceContext struct {
    // 基础设施
    InternalMysqlReader *gorm.DB
    InternalMysqlWriter *gorm.DB
    Rds                 redis.UniversalClient
    Sf                  syncx.SingleFlight
    Cache               *cache.Cache
    RabbitConsumer      rabbitmq.ListenerManger
    Scheduler           *scheduler.SchedulerMgr
    TaskQueue           *memory.TaskQueue

    // 业务服务
    DataVendorSvc     *DataVendorSvc
    MarketVendorSvc   *MarketVendorSvc
    OddsLadderSvc     *OddsLadderSvc
    // ...更多服务
}
```

## 初始化模式

```go
func (s *ServiceContext) Gen(init bool) {
    // 初始化基础设施
    s.Sf = syncx.NewSingleFlight()
    s.Cache = cache.New(time.Minute*5, time.Minute+time.Second*5)
    s.Scheduler = scheduler.NewSchedulerMgr()
    s.TaskQueue = memory.NewTaskQueue()

    // 初始化业务服务
    s.DataVendorSvc = NewDataVendorSvc(s)
    s.MarketVendorSvc = NewMarketVendorSvc(s)
    s.OddsLadderSvc = NewOddsLadderSvc(s)

    // 初始化数据库表（如需要）
    s.ShardSvc.init(context.Background())
    snowflake.IdGenRunFunc = s.GenSequence

    // 初始化数据（可选）
    if init {
        _ = s.DataVendorSvc.init(context.Background())
        _ = s.MarketVendorSvc.init(context.Background())
        s.ShardSvc.run(context.Background())

        // 声明 RabbitMQ 队列
        _ = s.RabbitSender.Declare(cst.OddsDataChangeMqTopic)
        _ = s.RabbitSender.Declare(cst.FixtureBindChangeMqTopic)
    }
}
```

## Service 层规范

### 标准 Service 结构

```go
type DataVendorSvc struct {
    svcCtx *ServiceContext
}

func NewDataVendorSvc(ctx *ServiceContext) *DataVendorSvc {
    return &DataVendorSvc{
        svcCtx: ctx,
    }
}

func (s *DataVendorSvc) init(ctx context.Context) error {
    // 初始化逻辑
    return nil
}

// 带缓存的查询
func (s *DataVendorSvc) GetById(ctx context.Context, id string) (*Entity, error) {
    return cache.RdsMultiGet[Entity](ctx, s.svcCtx.Rds, []string{id}, keyGen, func(ctx context.Context, ids []string) ([]Entity, error) {
        var data []Entity
        err := s.svcCtx.InternalMysqlReader.WithContext(ctx).
            Table(TableName).
            Where("id in ?", ids).
            Find(&data).Error
        return data, err
    })
}
```

## 缓存查询模式

```go
func (s *DataVendorSvc) ListByIds(ctx context.Context, ids []string) ([]Entity, error) {
    return cache.RdsMultiGet[Entity](ctx, s.svcCtx.Rds, ids, keyGen, func(ctx context.Context, queryIds []string) ([]Entity, error) {
        var data []Entity
        err := s.svcCtx.InternalMysqlReader.WithContext(ctx).
            Table(TableName).
            Where("id in ?", queryIds).
            Find(&data).Error
        return data, err
    }, time.Hour)
}
```

## 事务 + 缓存清理模式

```go
func (s *DataVendorSvc) Update(ctx context.Context, id string, updateMap map[string]any) error {
    // afterDeal 模式：事务操作 + 缓存清理
    _, err := s.afterDeal(ctx, id, func() error {
        updateMap["update_time"] = time.Now().UnixMilli()
        return s.svcCtx.InternalMysqlWriter.WithContext(ctx).Transaction(func(tx *gorm.DB) error {
            if sqlErr := tx.Table(TableName).
                Where("id = ?", id).
                Updates(updateMap).Error; sqlErr != nil {
                return sqlErr
            }
            return nil
        })
    })
    return err
}

func (s *DataVendorSvc) afterDeal(ctx context.Context, id string, fn func() error) (string, error) {
    // 查询关联 ID
    var belongId string
    if err := s.svcCtx.InternalMysqlReader.WithContext(ctx).Table(TableName).
        Select("belong_id").
        Where("id = ?", id).
        Scan(&belongId).Error; err != nil {
        return "", err
    }

    // 执行事务
    if err := fn(); err != nil {
        return "", err
    }

    // 清理缓存
    if len(belongId) > 0 {
        doCtx := trace.ContextWithSpanContext(context.Background(), trace.SpanContextFromContext(ctx))
        s.svcCtx.Rds.Del(doCtx, cacheKey.Build(belongId))
        return belongId, nil
    }
    return "", nil
}
```

## errgroup 并发初始化

```go
type generalTaskSvc struct{}

func (s *generalTaskSvc) Run(ctx context.Context) {
    eg, egCtx := errgroup.WithContext(ctx)

    eg.Go(func() error {
        _, err := ContextSvc.DataVendorSvc.doGetAll(egCtx)
        if err != nil {
            logx.WithContext(egCtx).Error(err)
        }
        return nil
    })

    eg.Go(func() error {
        _, err := ContextSvc.MarketVendorSvc.doGetAll(egCtx)
        if err != nil {
            logx.WithContext(egCtx).Error(err)
        }
        return nil
    })

    eg.Go(func() error {
        _, err := ContextSvc.OddsLadderSvc.doGetAll(egCtx)
        if err != nil {
            logx.WithContext(egCtx).Error(err)
        }
        return nil
    })

    _ = eg.Wait()
}
```

## RabbitMQ 消费者模式

```go
// Job 接口 + RabbitMQ Listener 组合
func (s *generalTaskSvc) Consume(ctx context.Context, message string, msgTime time.Time) error {
    var err error
    switch message {
    case cst.LocalCacheDataVendorKey:
        _, err = ContextSvc.DataVendorSvc.doGetAll(ctx)
    case cst.LocalCacheMarketVendorKey:
        _, err = ContextSvc.MarketVendorSvc.doGetAll(ctx)
    }
    return nil
}

func (s *generalTaskSvc) Config() rabbitmq.RabbitListenerConf {
    return rabbitmq.RabbitListenerConf{
        RabbitConf: ContextSvc.RabbitConf,
        Exchange:   cst.OddsDataChangeMqTopic,
        PreFetch:   16,
        Count:      4,
        Exclusive:  true,
        Ack:        true,
    }
}

// Job 定时接口
func (s *generalTaskSvc) DelayTime() time.Duration {
    return time.Minute
}

func (s *generalTaskSvc) PeriodTime() time.Duration {
    return time.Minute
}
```

## 批量操作模式

```go
// 批量插入
func (s *DataVendorSvc) BatchCreate(ctx context.Context, data []*Entity) error {
    if len(data) == 0 {
        return nil
    }
    return s.svcCtx.InternalMysqlWriter.WithContext(ctx).
        Table(TableName).
        CreateInBatches(data, 200).Error
}

// ON CONFLICT Upsert
func (s *MultiNameSvc) Replace(ctx context.Context, in *entity.MultiNames) error {
    nameCols := []string{"name_en", "name_cn", "name_tw", "name_id", "name_vn"}
    return s.svcCtx.InternalMysqlWriter.WithContext(ctx).
        Clauses(clause.OnConflict{
            Columns:   []clause.Column{{Name: "id"}, {Name: "type"}},
            DoUpdates: clause.AssignmentColumns(nameCols),
        }).
        Create(in).Error
}
```

## 雪花 ID 生成

```go
func (s *DataVendorSvc) Create(ctx context.Context, in *Entity) error {
    id, err := snowflake.IdTypeGenRandom(businessType, typeIndex)
    if err != nil {
        return err
    }
    in.Id = id
    in.CreateTime = time.Now().UnixMilli()
    in.UpdateTime = in.CreateTime
    return s.svcCtx.InternalMysqlWriter.WithContext(ctx).Table(TableName).Create(in).Error
}
```

## 多语言列处理

```go
func (s *MultiNameSvc) GetLangColumn(ctx context.Context, dbName, value string, inSet ...any) (sql string, valSet []any) {
    var sqlBuilder strings.Builder
    finalVal := "%" + value + "%"

    columns := []string{"name_en", "name_cn", "name_tw", "name_id", "name_vn", "name_ja", "name_ko", "name_th"}

    sqlBuilder.WriteString("(")
    for i, column := range columns {
        if i > 0 {
            sqlBuilder.WriteString(" or ")
        }
        sqlBuilder.WriteString(fmt.Sprintf("%s.%s like ?", dbName, column))
        valSet = append(valSet, finalVal)
    }
    sqlBuilder.WriteString(")")
    sql = sqlBuilder.String()
    valSet = append(inSet, valSet...)
    return
}
```

## 服务调用触发模式

```go
// 配置变更触发下游服务
func (s *DataVendorSvc) ConfigTrigger(ctx context.Context, data map[string][]string, businessType int32, opType int32, operator, ip string) error {
    return ContextSvc.RabbitSender.Send(ctx, topic, "", strUtl.ToJson(model.MatchBindChange{
        TeamIds: teamIds,
        CompIds: compIds,
    }))
}
```

## 工作流程总结

1. **定义 ServiceContext** - 注入所有服务依赖
2. **创建 Service 结构** - 组合 ServiceContext
3. **实现缓存查询** - RdsMultiGet + 回源
4. **事务操作** - afterDeal 模式
5. **初始化服务** - errgroup 并发加载
6. **消息处理** - Job + Listener 组合

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
