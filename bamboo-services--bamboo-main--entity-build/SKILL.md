---
name: entity-build
description: 根据口头描述快速生成符合项目规范的 Go Entity 代码。当用户说"创建一个实体"、"添加 entity"、"新建数据模型"或类似请求时自动激活。 Use when this capability is needed.
metadata:
  author: bamboo-services
---

# Entity 构建技能 (fyl-entity-build)

嘿~ 需要创建新的 Entity 吗？只需口头描述一下，我就能帮你生成符合 frontleaves-yggleaf 项目规范的 Go 实体代码！ (´∀｀)

## 快速开始

```
fyl-entity-build 创建一个 Player 实体
```

然后我会根据项目规范询问你需要的字段和配置，或者你可以直接告诉我：

```
fyl-entity-build 创建一个 Player 实体，包含 UUID、Name、Level 字段，属于 User
```

---

## 交互流程

当你说「创建一个实体」时，我会：

1. **收集基本信息**
    - 实体名称（自动转为 PascalCase）
    - 实体描述（中文）

2. **询问字段定义**
    - 字段名、类型、是否可空
    - 特殊要求（唯一索引、默认值等）

3. **询问关联关系**（可选）
    - 是否属于其他实体（belongs_to）
    - 是否拥有多个子实体（has_many）

4. **生成代码**
    - 输出到 `internal/entity/<snake_case>.go`
    - 所有字段行必须追加行尾注释（`// 中文说明`）
    - 提醒 Gene 常量定义

---

## 支持的字段类型

| 描述                       | Go 类型                    | 说明      |
|--------------------------|--------------------------|---------|
| `string`                 | `string`                 | 字符串     |
| `int`                    | `int`                    | 整数      |
| `int64`                  | `int64`                  | 64位整数   |
| `uint`                   | `uint`                   | 无符号整数   |
| `xSnowflake.SnowflakeID` | `xSnowflake.SnowflakeID` | 雪花算法 ID |
| `bool`                   | `bool`                   | 布尔值     |
| `float`                  | `float64`                | 浮点数     |
| `time`                   | `time.Time`              | 时间戳     |
| `decimal`                | `float64`                | 小数      |

---

## 使用 AskUserQuestion 收集信息

当用户的描述不够完整时，使用 `AskUserQuestion` 工具主动询问：

### 常见询问场景

```yaml
# 询问字段类型
questions:
  - question: "UUID 字段需要唯一约束吗？"
    header: "唯一约束"
    options:
      - label: "是，唯一"
        description: "添加 unique 约束，防止重复"
      - label: "否，可重复"
        description: "允许相同值存在"
    multiSelect: false

# 询问是否可空
questions:
  - question: "LastSeen 字段是否可空？"
    header: "可空类型"
    options:
      - label: "可空"
        description: "使用 *time.Time 指针类型"
      - label: "不可空"
        description: "使用 time.Time 类型"
    multiSelect: false

# 询问关联关系
questions:
  - question: "Player 需要关联哪些实体？"
    header: "关联关系"
    options:
      - label: "属于 User"
        description: "添加 UserID 外键，属于一个用户"
      - label: "拥有多个 GameProfile"
        description: "一对多关系"
    multiSelect: true
```

### 询问时机

| 情况       | 询问内容                        |
|----------|-----------------------------|
| 字段类型不明确  | 确认 Go 类型（string/int/bool 等） |
| 字段约束不明确  | 确认是否 unique、not null、默认值    |
| 关系不明确    | 确认是否属于其他实体、是否有一对多关系         |
| Gene 不明确 | 确认使用内置 Gene 还是自定义           |

---

## 常用字段模板

| 场景     | GORM 标签                                                 | JSON 标签                       |
|--------|---------------------------------------------------------|-------------------------------|
| 非空字符串  | `gorm:"not null;type:varchar(255);comment:说明"`          | `json:"field_name"`           |
| 可空字符串  | `gorm:"type:varchar(512);comment:说明"`                   | `json:"field_name,omitempty"` |
| 唯一字符串  | `gorm:"unique;not null;type:varchar(36);comment:说明"`    | `json:"field_name"`           |
| 整数     | `gorm:"not null;default:1;comment:说明"`                  | `json:"field_name"`           |
| 布尔     | `gorm:"not null;type:boolean;default:false;comment:说明"` | `json:"field_name"`           |
| 时间戳    | `gorm:"type:timestamptz;comment:说明"`                    | `json:"field_name,omitempty"` |
| 外键     | `gorm:"not null;index:idx_user_id;comment:说明"`          | `json:"user_id"`              |
| 密码（敏感） | `gorm:"not null;type:varchar(255);comment:说明"`          | `json:"-"`                    |

---

## 字段行尾注释规范（新增）

生成实体时，所有字段都必须遵循以下格式：

```go
FieldName FieldType `gorm:"...;comment:字段说明" json:"field_name"` // 字段说明
```

### 强制规则

1. **MUST**: 结构体中每一行字段定义都要有行尾注释（包括普通字段、外键字段、切片关联字段）。
2. **MUST**: 行尾注释语义必须和字段含义一致，建议与 `gorm comment` 保持一致。
3. **MUST**: 行尾注释使用中文，格式统一为 `// 中文说明`。
4. **DO NOT**: 省略行尾注释，即使字段名已经很清晰。

---

## 外键关系模板

### belongs_to（多对一）

```go
UserID xSnowflake.SnowflakeID `gorm:"not null;index:idx_user_id;comment:关联用户ID" json:"user_id"` // 关联用户ID
User   User                   `gorm:"constraint:OnDelete:CASCADE;comment:关联用户" json:"user,omitempty"` // 关联用户
```

### has_many（一对多）

```go
GameProfiles []GameProfile `gorm:"foreignKey:UserID;constraint:OnDelete:CASCADE;comment:游戏档案关联" json:"game_profiles,omitempty"` // 游戏档案关联
```

---

## GetGene 方法模板

```go
// GetGene 返回 xSnowflake.Gene，用于标识该实体在 ID 生成时使用的基因类型。
func (_ *EntityName) GetGene() xSnowflake.Gene {
return xSnowflake.GeneUser // 内置类型
// 或 return bConst.GeneForXXX  // 自定义类型（需要在 constant 中定义）
}
```

### 常用 Gene 类型

| Gene 值                      | 用途       |
|-----------------------------|----------|
| `xSnowflake.GeneUser`       | 用户实体     |
| `xSnowflake.GeneDefault`    | 默认/通用实体  |
| `bConst.GeneForGameProfile` | 游戏档案（32） |

---

## 完整生成示例

### 用户输入

```
创建一个 Player 实体，包含：
- UUID（唯一）
- Name（游戏内玩家名）
- Level（等级，默认1）
- LastSeen（最后在线时间，可空）
属于 User
```

### 生成结果

**文件**: `internal/entity/player.go`

```go
package entity

import (
	"time"

	xModels "github.com/bamboo-services/bamboo-base-go/major/models"
	xSnowflake "github.com/bamboo-services/bamboo-base-go/common/snowflake"
	bConst "github.com/frontleaves-mc/frontleaves-yggleaf/internal/constant"
)

// Player 玩家实体，包含 UUID、名称、等级等游戏内信息。
type Player struct {
	xModels.BaseEntity                        // 嵌入基础实体字段
	UserID             xSnowflake.SnowflakeID `gorm:"not null;index:idx_user_id;comment:关联用户ID" json:"user_id"` // 关联用户ID
	UUID               string                 `gorm:"unique;not null;type:varchar(36);comment:Minecraft UUID" json:"uuid"` // Minecraft UUID
	Name               string                 `gorm:"not null;type:varchar(32);comment:游戏内玩家名" json:"name"` // 游戏内玩家名
	Level              int                    `gorm:"not null;default:1;comment:玩家等级" json:"level"` // 玩家等级
	LastSeen           *time.Time             `gorm:"type:timestamptz;comment:最后在线时间" json:"last_seen,omitempty"` // 最后在线时间

	// ----------
	//  外键约束
	// ----------
	User User `gorm:"constraint:OnDelete:CASCADE;comment:关联用户" json:"user,omitempty"` // 关联用户
}

// GetGene 返回 xSnowflake.Gene，用于标识该实体在 ID 生成时使用的基因类型。
func (_ *Player) GetGene() xSnowflake.Gene {
	return bConst.GeneForPlayer // 需要在 internal/constant/gene_number.go 中定义
}

```

**提醒**: 记得在 `internal/constant/gene_number.go` 中添加 Gene 常量：

```go
const (
GeneForGameProfile xSnowflake.Gene = 32 // 游戏档案
GeneForPlayer      xSnowflake.Gene = 64 // 新增
)
```

---

## 注意事项 💖

1. **Gene 常量**: 自定义 Gene 需要在 `internal/constant/gene_number.go` 中定义
2. **外键删除策略**: 默认使用 `OnDelete:CASCADE`
3. **字段行尾注释**: 所有字段定义必须追加 `// 中文说明`，不可省略
4. **指针类型**: 可空字段自动添加 `omitempty` JSON 标签
5. **敏感字段**: 密码等使用 `json:"-"` 隐藏
6. **不确定时**: 使用 AskUserQuestion 询问用户，不要擅自猜测
7. **新表时刻**: 若创建全新的表，需要写入 internal/startup/startup_database.go 的 AutoMigrate

---

## 参考资料

- **bamboo-base-go 全局文档**: https://doc.x-lf.com/llms.txt
- **具体路径查询**: https://doc.x-lf.com/llms.mdx/<search_path>

### 查询示例

| 需要查找的内容           | 查询 URL                                              |
|-------------------|-----------------------------------------------------|
| BaseEntity 定义     | https://doc.x-lf.com/llms.mdx/models/base_entity.go |
| Snowflake Gene 类型 | https://doc.x-lf.com/llms.mdx/snowflake/gene.go     |
| 所有可导出类型           | https://doc.x-lf.com/llms.txt                       |

嘿嘿~ 开始创建你的 Entity 吧！＼(^o^)／

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bamboo-services) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
