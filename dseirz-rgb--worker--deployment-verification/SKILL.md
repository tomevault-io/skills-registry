---
name: deployment-verification
description: | Use when this capability is needed.
metadata:
  author: dseirz-rgb
---

# Deployment Verification (部署验证)

> 🚀 **核心理念**: 部署不是终点，验证才是。未经验证的部署等于没有部署。

## 🔴 第一原则：部署后必须验证

**任何部署都必须经过完整验证流程！**

```
❌ 错误思路: "代码推送了，应该没问题"
✅ 正确思路: "代码推送了，让我验证一下部署状态和功能"

❌ 错误思路: "本地测试通过了，线上肯定也行"  
✅ 正确思路: "本地测试通过了，但线上环境不同，必须验证"
```

**验证优先级**: 健康检查 > 核心功能 > 边缘场景 > 性能指标

## When to Use This Skill

使用此技能当你需要：
- 部署代码到 Vercel 或 GCP Cloud Run
- 验证部署是否成功
- 检查环境变量是否正确同步
- 排查部署失败的问题
- 确认 CI/CD 流程正常运行
- 验证健康检查端点

## Not For / Boundaries

此技能不适用于：
- 本地开发环境调试
- 单元测试编写
- 代码审查流程

---

## Quick Reference

### 🎯 部署验证工作流

```
代码修改 → 本地测试 → Git Commit → Git Push → 等待构建 → 验证部署 → 功能测试 → 完成
              ↓                                    ↓
           失败 → 修复                          失败 → 查日志 → 修复 → 重新部署
```

### 📋 部署前必查清单

| 检查项 | 命令/操作 | 通过标准 |
|--------|----------|----------|
| 本地构建 | `npm run build` | 无错误 |
| 本地测试 | `npm test` | 全部通过 |
| 类型检查 | `npm run typecheck` | 无错误 |
| Lint 检查 | `npm run lint` | 无错误 |
| 环境变量 | 对比 `.env` 和线上 | 完全同步 |

### ✅ 部署后必验清单

| 验证项 | 方法 | 通过标准 |
|--------|------|----------|
| 部署状态 | 查看 CI/CD 日志 | 状态为成功 |
| 健康检查 | `curl /api/health` | 返回 200 |
| 核心功能 | Playwright 测试 | 功能正常 |
| 控制台日志 | 浏览器 DevTools | 无错误 |
| 网络请求 | Network 面板 | 无失败请求 |

---

## 部署验证工作流

### Phase 1: 部署前准备

```bash
# 1. 确保代码质量
npm run build        # 构建检查
npm run test         # 测试检查
npm run lint         # 代码规范检查

# 2. 检查环境变量同步
# 本地环境变量
grep -E "^[A-Z]" .env | cut -d= -f1 | sort > /tmp/local-env.txt

# Vercel 环境变量
vercel env ls | grep -E "^[A-Z]" | awk '{print $1}' | sort > /tmp/vercel-env.txt

# 对比差异
diff /tmp/local-env.txt /tmp/vercel-env.txt

# 3. 提交代码
git add .
git commit -m "feat: 描述性提交信息"
git push origin main
```

### Phase 2: 监控部署过程

```bash
# Vercel 部署监控
vercel ls --limit 5  # 查看最近部署

# 或通过 GitHub Actions
gh run list --limit 5
gh run view <run-id>
```

### Phase 3: 部署后验证

```bash
# 1. 健康检查
curl -s https://your-app.vercel.app/api/health | jq

# 2. 核心 API 测试
curl -s https://your-app.vercel.app/api/your-endpoint | jq

# 3. Playwright 功能测试
npx playwright test --project=chromium tests/e2e/smoke.spec.ts
```

---

## Vercel 部署验证清单

### 🔍 部署状态检查

```bash
# 查看部署列表
vercel ls

# 查看部署详情
vercel inspect <deployment-url>

# 查看部署日志
vercel logs <deployment-url>
```

### 📊 Vercel 仪表板检查

1. **Deployments 页面**
   - 确认最新部署状态为 "Ready"
   - 检查构建时间是否正常
   - 查看是否有构建警告

2. **Functions 页面**
   - 确认 Serverless Functions 正常运行
   - 检查函数执行时间
   - 查看错误率

3. **Analytics 页面**
   - 检查请求成功率
   - 查看响应时间分布
   - 确认无异常流量

### ⚠️ 常见 Vercel 部署问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 构建失败 | 依赖问题 | 检查 `package.json`，清除缓存重试 |
| 环境变量缺失 | 未同步 | `vercel env add <name>` |
| 函数超时 | 执行时间过长 | 优化代码或升级套餐 |
| 404 错误 | 路由配置 | 检查 `vercel.json` 和路由设置 |
| CORS 错误 | 跨域配置 | 添加正确的 CORS 头 |

### 🛠️ Vercel 验证脚本

```bash
# 使用内置脚本
bash .kiro/skills/deployment-verification/scripts/verify-vercel.sh
```

---

## GCP Cloud Run 部署验证清单

### 🔍 部署状态检查

```bash
# 查看服务列表
gcloud run services list

# 查看服务详情
gcloud run services describe <service-name> --region=<region>

# 查看最近修订版本
gcloud run revisions list --service=<service-name>

# 查看日志
gcloud logging read "resource.type=cloud_run_revision AND resource.labels.service_name=<service-name>" --limit=50
```

### 📊 GCP Console 检查

1. **Cloud Run 服务页面**
   - 确认服务状态为绿色
   - 检查最新修订版本
   - 查看流量分配

2. **Metrics 页面**
   - 请求计数和延迟
   - 容器实例数
   - 内存和 CPU 使用率

3. **Logs 页面**
   - 应用日志
   - 系统日志
   - 错误日志

### ⚠️ 常见 GCP 部署问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 部署失败 | 镜像构建错误 | 检查 Dockerfile |
| 启动失败 | 端口配置 | 确保监听 `$PORT` |
| 冷启动慢 | 容器初始化 | 优化启动时间或设置最小实例 |
| 内存不足 | 资源限制 | 增加内存配置 |
| 权限错误 | IAM 配置 | 检查服务账号权限 |

### 🛠️ GCP 验证脚本

```bash
# 使用内置脚本
bash .kiro/skills/deployment-verification/scripts/verify-gcp.sh <service-name> <region>
```

---

## 环境变量同步检查

### 🔑 环境变量分类

| 类别 | 示例 | 同步要求 |
|------|------|----------|
| API 密钥 | `GEMINI_API_KEY`, `OPENAI_API_KEY` | 必须同步 |
| 数据库 | `DATABASE_URL`, `SUPABASE_*` | 必须同步 |
| 第三方服务 | `LIVEKIT_*`, `STRIPE_*` | 必须同步 |
| 应用配置 | `NEXT_PUBLIC_*` | 必须同步 |
| 开发专用 | `DEBUG`, `LOG_LEVEL` | 可选同步 |

### 📋 同步检查流程

```bash
# 1. 导出本地环境变量名
grep -E "^[A-Z]" .env | cut -d= -f1 | sort

# 2. 导出 Vercel 环境变量名
vercel env ls

# 3. 手动对比或使用脚本
# 参考: references/env-sync-checklist.md
```

### ⚠️ 常见遗漏的环境变量

```
# AI 服务
GEMINI_API_KEY
OPENAI_API_KEY
ANTHROPIC_API_KEY

# 实时通信
LIVEKIT_URL
LIVEKIT_API_KEY
LIVEKIT_API_SECRET

# 数据库
DATABASE_URL
SUPABASE_URL
SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY

# 认证
NEXTAUTH_SECRET
NEXTAUTH_URL

# 支付
STRIPE_SECRET_KEY
STRIPE_WEBHOOK_SECRET
```

---

## 健康检查端点验证

### 📍 标准健康检查端点

```typescript
// /api/health.ts
export async function GET() {
  try {
    // 检查数据库连接
    await db.query('SELECT 1');
    
    // 检查外部服务
    const services = {
      database: 'ok',
      cache: await checkCache(),
      external: await checkExternalAPI(),
    };
    
    return Response.json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      services,
    });
  } catch (error) {
    return Response.json({
      status: 'unhealthy',
      error: error.message,
    }, { status: 503 });
  }
}
```

### 🔍 健康检查验证

```bash
# 基本健康检查
curl -s https://your-app.vercel.app/api/health | jq

# 预期响应
{
  "status": "healthy",
  "timestamp": "2025-01-01T00:00:00.000Z",
  "services": {
    "database": "ok",
    "cache": "ok",
    "external": "ok"
  }
}

# 带超时的检查
curl -s --max-time 10 https://your-app.vercel.app/api/health

# 检查响应状态码
curl -s -o /dev/null -w "%{http_code}" https://your-app.vercel.app/api/health
```

---

## 故障排查指南

### 🔴 部署失败

```bash
# 1. 查看构建日志
vercel logs <deployment-url> --output=raw

# 2. 本地复现
npm run build 2>&1 | tee build.log

# 3. 检查依赖
npm ls --depth=0
npm audit

# 4. 清除缓存重试
vercel --force
```

### 🟡 部署成功但功能异常

```bash
# 1. 检查环境变量
vercel env ls

# 2. 检查函数日志
vercel logs <deployment-url> --follow

# 3. 检查网络请求
# 使用浏览器 DevTools Network 面板

# 4. Playwright 调试
npx playwright test --debug
```

### 🟠 性能问题

```bash
# 1. 检查冷启动时间
curl -w "@curl-format.txt" -s https://your-app.vercel.app/api/health

# 2. 检查函数执行时间
# Vercel Dashboard > Functions > 查看执行时间

# 3. 检查资源使用
# GCP Console > Cloud Run > Metrics
```

---

## Examples

### Example 1: Vercel 部署验证

**场景:** 推送代码到 main 分支后验证部署

**Steps:**
```bash
# 1. 推送代码
git push origin main

# 2. 等待部署完成 (约 1-2 分钟)
sleep 120

# 3. 检查部署状态
vercel ls --limit 1

# 4. 健康检查
curl -s https://your-app.vercel.app/api/health | jq

# 5. Playwright 功能测试
npx playwright test tests/e2e/smoke.spec.ts
```

**Expected Output:**
```
✅ 部署状态: Ready
✅ 健康检查: 200 OK
✅ 功能测试: 全部通过
```

### Example 2: 环境变量同步

**场景:** 添加新的 API 密钥

**Steps:**
```bash
# 1. 添加到本地 .env
echo "NEW_API_KEY=xxx" >> .env

# 2. 添加到 Vercel
vercel env add NEW_API_KEY production
# 输入值: xxx

# 3. 验证同步
vercel env ls | grep NEW_API_KEY

# 4. 重新部署以应用新变量
vercel --prod
```

### Example 3: 排查部署失败

**场景:** 部署失败，需要排查原因

**Steps:**
```bash
# 1. 查看部署状态
vercel ls --limit 5

# 2. 查看失败日志
vercel logs <failed-deployment-url>

# 3. 本地复现
npm run build

# 4. 修复问题后重新部署
git add .
git commit -m "fix: 修复构建错误"
git push origin main
```

---

## References

- `references/env-sync-checklist.md`: 环境变量同步检查清单
- `scripts/verify-vercel.sh`: Vercel 部署验证脚本
- `scripts/verify-gcp.sh`: GCP Cloud Run 验证脚本

---

## Maintenance

- **Sources**: Vercel 官方文档, GCP Cloud Run 文档, 项目实践经验
- **Last Updated**: 2025-01-01
- **Known Limits**: 
  - 脚本依赖 `vercel` CLI 和 `gcloud` CLI
  - 某些验证需要相应的访问权限

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dseirz-rgb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
