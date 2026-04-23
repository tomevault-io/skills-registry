---
name: snowflake-id-skill
description: This skill should be used when the user asks to "generate unique ID", "snowflake algorithm", "distributed ID", "parse ID", "雪花算法 ID", "生成全局唯一ID", "ID 解析", or needs to generate globally unique IDs using snowflake algorithm with business type encoding, distributed unique ID generation, and ID parsing to retrieve business information. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 雪花算法 ID 生成

## ID 生成配置

```go
const (
    nodeBits uint8 = 10          // 节点位数
    tableTypeBits uint8 = 7      // 业务类型位数
    insideType uint8 = nodeBits - tableTypeBits  // 内部类型位数

    businessMax = -1 ^ (-1 << tableTypeBits)  // 业务类型最大值
    typeMax = -1 ^ (-1 << insideType)         // 类型索引最大值
)

const (
    intBusinessMin = 10
    intBusinessMax = 31
    intTypeMax = 3
    intTypeKey = 4
)
```

## 随机模式生成 ID

```go
var (
    locker    sync.RWMutex
    lockerMap = map[int32]*snowflake.Node{}
)

func IdTypeGenRandom(businessType, typeIndex int32) (id string, err error) {
    if businessType == 0 || businessType > businessMax || typeIndex > typeMax {
        err = InputValueErr
        return
    }
    // 计算节点索引
    nodeIndex := businessType<<insideType + typeIndex

    // 单例模式获取节点
    locker.RLock()
    node, ok := lockerMap[nodeIndex]
    locker.RUnlock()
    if !ok {
        locker.Lock()
        node, ok = lockerMap[nodeIndex]
        if !ok {
            node, _ = snowflake.NewNode(int64(nodeIndex))
            lockerMap[nodeIndex] = node
        }
        locker.Unlock()
    }

    // 生成 Base32 编码的 ID
    id = node.Generate().Base32()
    return
}
```

## 自增模式生成 ID

```go
func IdTypeGenInc(ctx context.Context, businessType, typeIndex int32) (id string, err error) {
    if businessType < intBusinessMin || businessType > intBusinessMax || typeIndex > intTypeMax {
        err = InputValueErr
        return
    }

    // 从数据库或 Redis 获取自增序列
    idInt, err := IdGenRunFunc(ctx, int(businessType))
    if err != nil {
        return
    }

    // 组合节点部分
    nodePart := int((businessType-intBusinessMin)*intTypeKey + typeIndex + intBusinessMin)
    id = strconv.Itoa((nodePart * pow10) + idInt)
    return
}
```

## ID 解析

```go
func IdTypeParse(idStr string) (businessType, typeIndex int32, err error) {
    // 解析数字格式
    if len(idStr) >= intMinLen {
        nodePart, _ := strconv.Atoi(idStr[:intBaseLen])
        val2 := nodePart - intBusinessMin
        businessType = int32(val2/intTypeKey + intBusinessMin)
        typeIndex = int32(val2 % intTypeKey)
        return
    }

    // 解析 Base32 格式
    id, _ := strconv.ParseInt(idStr[2:], 16, 64)
    nodeId := (id >> 12) & 0x3FF
    businessType = int32(nodeId >> insideType)
    typeIndex = int32(nodeId & 0b111)
    return
}
```

## 使用示例

```go
// 生成订单 ID (业务类型 10，类型索引 0)
orderId, _ := IdTypeGenRandom(10, 0)

// 解析 ID
bizType, typeIdx, _ := IdTypeParse(orderId)
fmt.Printf("businessType: %d, typeIndex: %d\n", bizType, typeIdx)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
