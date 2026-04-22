---
name: business-log-skill
description: This skill should be used when the user asks to "implement business log", "operation audit", "change tracking", "data recovery", "记录操作日志", "操作审计", "变更追踪", or needs to implement business operation logs including operation recording, in-transaction saving, log query for auditing, change tracking, and data recovery scenarios. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 业务操作日志模式

## 日志实体定义

```go
type BusinessOpLog struct {
    BusinessId   string `gorm:"column:business_id;type:varchar(255);primaryKey;comment:业务id"`
    BusinessType int32  `gorm:"column:business_type;type:int;not null;comment:业务类型"`
    OperateType  int32  `gorm:"column:operate_type;type:int;not null;comment:操作类型"`
    Data         string `gorm:"column:data;type:text;comment:日志数据"`
    CreateBy     string `gorm:"column:create_by;type:varchar(64)"`
    CreateTime   int64  `gorm:"column:create_time;type:bigint"`
}

const (
    BusinessTypeTeam     int32 = 1
    BusinessTypeComp     int32 = 2
    BusinessTypeMatch    int32 = 3
)

const (
    OperateTypeCreate int32 = 1
    OperateTypeUpdate int32 = 2
    OperateTypeDelete int32 = 3
    OperateTypeBind   int32 = 4  // 关联操作
)
```

## 日志 Service

```go
type BusinessOpLogSvc struct {
    db *gorm.DB
}

func (s *BusinessOpLogSvc) TxSave(tx *gorm.DB, logs ...*BusinessOpLog) error {
    return tx.Table(TableNameBusinessOpLog).CreateInBatches(logs, 100).Error
}

func (s *BusinessOpLogSvc) Query(ctx context.Context, businessId string) ([]*BusinessOpLog, error) {
    var logs []*BusinessOpLog
    err := s.db.WithContext(ctx).Table(TableNameBusinessOpLog).
        Where("business_id = ?", businessId).
        Order("create_time desc").
        Find(&logs).Error
    return logs, err
}
```

## 事务内记录日志

```go
func (s *BaseJob) Run(ctx context.Context) {
    nowTime := time.Now().UnixMilli()

    err := s.svcCtx.InternalMysqlWriter.WithContext(ctx).Transaction(func(tx *gorm.DB) error {
        // 业务操作：创建记录
        if err := tx.Table("standard_team").Create(&entity.StandardTeam{
            TeamId:  stdId,
            SportId: sportId,
            CommonTime: entity.CommonTime{
                CreateBy:   "system",
                CreateTime: nowTime,
                UpdateBy:   "system",
                UpdateTime: nowTime,
            },
        }).Error; err != nil {
            return err
        }

        // 业务操作：更新关联
        if err := tx.Table("third_team").
            Where("team_id = ? and sport_id = ? and source = ?", teamId, sportId, source).
            Updates(map[string]any{
                "stand_team_id": stdId,
                "update_by":     "system",
                "update_time":   nowTime,
            }).Error; err != nil {
            return err
        }

        // 日志记录：在事务内保存
        var logs []*entity.BusinessOpLog
        logs = append(logs, &entity.BusinessOpLog{
            BusinessId:   stdId,
            BusinessType: BusinessTypeTeam,
            OperateType:  OperateTypeBind,
            Data: string(strUtl.ToJson(BindOperaLog{
                Key:    "auto_gen",
                NewSet: genIds,
            })),
            CreateBy:   "system",
            CreateTime: nowTime,
        })
        for _, genId := range genIds {
            logs = append(logs, &entity.BusinessOpLog{
                BusinessId:   genId,
                BusinessType: BusinessTypeTeam,
                OperateType:  OperateTypeBind,
                Data: string(strUtl.ToJson(ThirdBindOperaLog{
                    Key: "auto_gen",
                    New: stdId,
                })),
                CreateBy:   "system",
                CreateTime: nowTime,
            })
        }
        // 事务内保存日志
        if err := s.svcCtx.BusinessOpLogSvc.TxSave(tx, logs...); err != nil {
            return err
        }

        return nil
    })
    if err != nil {
        logx.WithContext(ctx).Error(err)
    }
}
```

## 日志数据结构

```go
// 标准数据绑定日志
type StdBindOperaLog struct {
    Key    string   `json:"key"`    // 操作标识
    NewSet []string `json:"newSet"` // 新关联的 ID 列表
}

// 三方数据绑定日志
type ThirdBindOperaLog struct {
    Key string `json:"key"`
    New string `json:"new"` // 关联到的标准 ID
}

// 通用变更日志
type ChangeOperaLog struct {
    Op       string `json:"op"`        // 操作类型
    OldValue any    `json:"oldValue"` // 旧值
    NewValue any    `json:"newValue"` // 新值
}

// 批量操作日志
type BatchOperaLog struct {
    Op     string `json:"op"`      // 操作类型
    IdList []string `json:"idList"` // 批量 ID
}
```

## 日志查询

```go
func QueryOpLogs(ctx context.Context, db *gorm.DB, businessId string) ([]*BusinessOpLog, error) {
    var logs []*BusinessOpLog
    err := db.WithContext(ctx).Table(TableNameBusinessOpLog).
        Where("business_id = ?", businessId).
        Order("create_time desc").
        Find(&logs).Error
    return logs, err
}

// 按类型查询
func QueryOpLogsByType(ctx context.Context, db *gorm.DB, businessType int32, startTime, endTime int64) ([]*BusinessOpLog, error) {
    var logs []*BusinessOpLog
    err := db.WithContext(ctx).Table(TableNameBusinessOpLog).
        Where("business_type = ? AND create_time >= ? AND create_time <= ?", businessType, startTime, endTime).
        Order("create_time desc").
        Find(&logs).Error
    return logs, err
}
```

## 日志解析

```go
func ParseOpLog(log *BusinessOpLog) (interface{}, error) {
    switch log.OperateType {
    case OperateTypeBind:
        if strings.Contains(log.Data, `"newSet"`) {
            var data StdBindOperaLog
            if err := json.Unmarshal([]byte(log.Data), &data); err != nil {
                return nil, err
            }
            return data, nil
        }
        var data ThirdBindOperaLog
        if err := json.Unmarshal([]byte(log.Data), &data); err != nil {
            return nil, err
        }
        return data, nil
    default:
        return log.Data, nil
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
