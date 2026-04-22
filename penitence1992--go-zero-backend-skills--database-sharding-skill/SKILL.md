---
name: database-sharding-skill
description: This skill should be used when the user asks to "implement database sharding", "table sharding by time", "shard query", "generate table name by time", "分表查询", "时间分表", "数据库分片", or needs to implement database sharding including time-based table partitioning (year/week), shard query services, table name generation by time, and cross-table query execution. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 数据库分片策略

## 时间分片类型

```go
type TimeShardType string

const (
    ShardTypeYear TimeShardType = "year"   // 按年分片
    ShardTypeWeek TimeShardType = "week"   // 按周分片
)

// 格式: {table}_y{year}  或  {table}_w{date}
```

## 根据时间戳生成表名

```go
func GetTableNameByTimestamp(table string, timeType TimeShardType, ts int64, cycles ...int) string {
    tm := time.UnixMilli(ts).In(time.Local)

    switch timeType {
    case ShardTypeYear:
        if len(cycles) > 0 && cycles[0] != 0 {
            tm = tm.AddDate(cycles[0], 0, 0)
        }
        return fmt.Sprintf("%s_y%s", table, tm.Format("2006"))

    case ShardTypeWeek:
        if len(cycles) > 0 && cycles[0] != 0 {
            tm = tm.AddDate(0, 0, 7*cycles[0])
        }
        monday := GetWeekMonday(tm)
        return fmt.Sprintf("%s_w%s", table, monday.Format("20060102"))
    }
    return table
}

func GetWeekMonday(t time.Time) time.Time {
    weekday := int(t.Weekday())
    if weekday == 0 {
        weekday = 7
    }
    monday := t.AddDate(0, 0, -weekday+1)
    return time.Date(monday.Year(), monday.Month(), monday.Day(), 0, 0, 0, 0, t.Location())
}
```

## 分表查询服务

```go
type ShardSvc struct {
    Db *gorm.DB
}

// 自动识别需要查询的表范围
func EventDataSetDo[T any](ctx context.Context, rDb *gorm.DB, baseTable string, shardType shard.TimeShardType, startTs, endTs int64) (lst []T, err error) {
    var (
        eg, egCtx = errgroup.WithContext(ctx)
        mu        sync.Mutex
    )

    // 计算需要查询的表
    tables := calculateTables(baseTable, shardType, startTs, endTs)

    for _, tableName := range tables {
        table := tableName
        eg.Go(func() error {
            var data []T
            err := rDb.WithContext(egCtx).
                Table(table).
                Where("create_time >= ? AND create_time <= ?", startTs, endTs).
                Find(&data).Error

            if err != nil {
                return err
            }
            mu.Lock()
            lst = append(lst, data...)
            mu.Unlock()
            return nil
        })
    }

    return eg.Wait()
}
```

## 使用示例

```go
// 查询最近一周的数据
startTime := time.Now().AddDate(0, 0, -7).UnixMilli()
endTime := time.Now().UnixMilli()

tables := []string{
    shard.GetTableNameByTimestamp("event_data", shard.ShardTypeWeek, startTime),
    shard.GetTableNameByTimestamp("event_data", shard.ShardTypeWeek, endTime),
}

// 或使用分表查询服务
results, err := shardSvc.EventDataSetDo[EventData](ctx, db, "event_data", shard.ShardTypeWeek, startTime, endTime)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
