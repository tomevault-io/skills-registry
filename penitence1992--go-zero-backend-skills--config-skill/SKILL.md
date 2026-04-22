---
name: config-skill
description: This skill should be used when the user asks to "load", "parse configuration config file", "read properties file", "manage application settings", "switch environment config", "configure application", "配置文件管理", "加载配置", "解析配置项", or needs to handle configuration management including .properties format, configuration loading, environment switching, or config struct definitions. Use when this capability is needed.
metadata:
  author: penitence1992
---

# 配置文件管理

## .properties 配置文件格式

```properties
Name=api-api
Port=31508
Mode=dev
Timeout=30000

# 数据库配置
InternalMysqlReader.Url=user01:password@tcp(192.168.4.14:3306)/odds_center?...
InternalMysqlReader.Debug=true
InternalMysqlReader.MaxOpen=100
InternalMysqlReader.MaxIdle=50

# Redis 配置
RedisConf.Host=192.168.4.7:30679
RedisConf.Pass=123456
RedisConf.Db=9

# RabbitMQ 配置
InternalRabbit.Url=amqp://guest:guest@192.168.4.8:5672
```

## 配置结构体定义

```go
type Config struct {
    rest.RestConf
    SourceRabbit        rabbitmq.RabbitConf `json:",optional"`
    InternalRabbit      rabbitmq.RabbitConf
    InternalMysqlReader cfg.MysqlConf
    InternalMysqlWriter cfg.MysqlConf
    RedisConf           cfg.RedisConf
    Pprof               cfg.PprofConf `json:",optional"`
    AliOss              cfg.AliOss
    AwsOss              cfg.AwsOss
    OssType             string `json:",optional"`
}
```

## 配置加载

```go
// api.go
var configFiles = []string{"api/etc/api.properties"}
flag.Func("f", "the config file", func(s string) error {
    configFiles = []string{s}
    return nil
})

var c config.Config
if err := cfg.LoadProperties(configFiles, &c); err != nil {
    panic(err)
}
```

## MySQL 配置

```go
type MysqlConf struct {
    Url     string
    MaxOpen int  `json:",optional"`
    MaxIdle int  `json:",default=50"`
    Debug   bool `json:",default=false"`
}
```

## Redis 配置

```go
type RedisConf struct {
    Host string
    Pass string `json:",optional"`
    Tls  bool   `json:",optional"`
    Db   int    `json:",default=0"`
    Node string `json:",default=node,options=node|cluster"`
}
```

## RabbitMQ 配置

```go
type RabbitConf struct {
    Url string
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
