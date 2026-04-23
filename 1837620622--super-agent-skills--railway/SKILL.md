---
name: railway
description: 在 Railway 平台上部署应用。适用于部署容器化应用、设置数据库、配置私有网络或管理 Railway 项目。触发关键词：Railway, railway.app, deploy container, Railway database, 容器部署。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# Railway 部署

在 Railway 平台上部署和管理应用。

## 快速开始

```bash
# 安装 Railway CLI
npm i -g @railway/cli

# 登录
railway login

# 初始化项目
railway init

# 部署
railway up

# 打开仪表盘
railway open
```

## railway.toml 配置文件

```toml
[build]
builder = "nixpacks"  # 或 "railpack"(Nixpacks 的继任者)
buildCommand = "npm run build"

[deploy]
preDeployCommand = ["npm run db:migrate"]  # 部署前命令
startCommand = "npm start"
healthcheckPath = "/health"
healthcheckTimeout = 300
restartPolicyType = "on_failure"  # never | on_failure | always
restartPolicyMaxRetries = 3
cronSchedule = "*/15 * * * *"  # 可选：定时任务

[service]
internalPort = 3000
```

## railway.json 配置文件（替代格式）

```json
{
  "$schema": "https://railway.com/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "npm run build"
  },
  "deploy": {
    "preDeployCommand": ["npm run db:migrate"],
    "startCommand": "npm start",
    "healthcheckPath": "/",
    "healthcheckTimeout": 100,
    "restartPolicyType": "on_failure",
    "cronSchedule": "0 0 * * *"
  }
}
```

## Nixpacks 配置

```toml
# nixpacks.toml
[variables]
NODE_ENV = "production"

[phases.setup]
nixPkgs = ["nodejs-18_x", "python311"]

[phases.install]
cmds = ["npm ci --only=production"]

[phases.build]
cmds = ["npm run build"]

[start]
cmd = "npm start"
```

## Railpack 配置（Nixpacks 的继任者）

Railpack 是 Railway 新一代构建工具，提供更快的构建速度和更好的缓存机制。

```toml
# railpack.toml
[build]
command = "npm run build"

[start]
command = "npm start"

[cache]
paths = ["node_modules", ".next/cache"]
```

## 环境变量

```bash
# 设置变量
railway variables set DATABASE_URL="postgres://..."

# 从文件设置
railway variables set < .env

# 链接到服务
railway service
railway variables set API_KEY="secret"
```

## 数据库服务

### PostgreSQL
```bash
# 添加 PostgreSQL
railway add -d postgres

# 获取连接字符串
railway variables get DATABASE_URL
```

### Redis
```bash
railway add -d redis
# 通过 REDIS_URL 访问
```

### MySQL
```bash
railway add -d mysql
# 通过 MYSQL_URL 访问
```

## 私有网络

Railway 提供内部 DNS 用于服务间通信：

```bash
# 内部连接字符串格式
# Format: {service-name}.railway.internal:{port}

# PostgreSQL 私有网络连接
postgresql://postgres:password@postgres.railway.internal:5432/railway

# Redis 私有网络连接
redis://default:password@redis.railway.internal:6379
```

环境变量配置：
```yaml
# 引用其他服务
DATABASE_HOST: ${{postgres.railway.internal}}
DATABASE_PORT: 5432
REDIS_URL: ${{Redis.REDIS_URL}}
```

## 卷（持久化存储）

```bash
# 创建卷
railway volume create my-data

# 挂载到服务
railway volume attach my-data:/app/data
```

在代码中：
```javascript
// 数据在部署之间持久化
const dataPath = '/app/data';
fs.writeFileSync(`${dataPath}/file.json`, JSON.stringify(data));
```

## 定时任务

```toml
# railway.toml
[deploy]
startCommand = "node cron.js"
cronSchedule = "0 */6 * * *"  # 每 6 小时
```

## 多服务设置

```
my-project/
├── api/
│   ├── railway.toml
│   └── ...
├── worker/
│   ├── railway.toml
│   └── ...
└── frontend/
    ├── railway.toml
    └── ...
```

部署每个服务：
```bash
cd api && railway up
cd ../worker && railway up
cd ../frontend && railway up
```

## Dockerfile 部署

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

```toml
# railway.toml
[build]
builder = "dockerfile"
dockerfilePath = "./Dockerfile"
```

## 健康检查

```typescript
// Express 健康检查端点
app.get('/health', (req, res) => {
  res.status(200).json({ 
    status: 'healthy',
    timestamp: new Date().toISOString()
  });
});
```

```toml
# railway.toml
[deploy]
healthcheckPath = "/health"
healthcheckTimeout = 100
```

## 水平扩展与副本

Railway 支持水平扩展，可以部署多个副本：

```json
{
  "$schema": "https://railway.com/railway.schema.json",
  "deploy": {
    "numReplicas": 3
  }
}
```

### 多区域部署

跨多个区域部署以提高可用性和性能：

```json
{
  "$schema": "https://railway.com/railway.schema.json",
  "deploy": {
    "multiRegionConfig": {
      "us-west2": { "numReplicas": 2 },
      "us-east4-eqdc4a": { "numReplicas": 2 },
      "europe-west4-drams3a": { "numReplicas": 2 },
      "asia-southeast1-eqsg3a": { "numReplicas": 2 }
    }
  }
}
```

## TCP 代理

为非 HTTP 服务（如数据库）配置 TCP 代理：

```bash
# 在服务设置中配置 TCP 代理后，Railway 会提供：
# - 唯一域名和端口
# - 自动负载均衡到最近区域的副本

# 连接示例（数据库）
postgresql://user:pass@your-service.proxy.rlwy.net:12345/railway
```

## 自定义域名

```bash
# 添加自定义域名
railway domain add example.com

# Railway 自动处理 SSL 证书
# 需要在 DNS 中添加 CNAME 记录指向 Railway 提供的域名
```

## 相关资源

- **Railway 文档**: https://docs.railway.app
- **Railway CLI**: https://docs.railway.app/develop/cli
- **扩展指南**: https://docs.railway.app/reference/scaling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
