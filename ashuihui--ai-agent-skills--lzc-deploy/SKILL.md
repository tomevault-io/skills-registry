---
name: lzc-deploy
description: | Use when this capability is needed.
metadata:
  author: ashuihui
---

# 懒猫微服部署助手

欢迎使用懒猫微服（Lazycat Microservice）部署助手！本 skill 将帮助你将应用部署到懒猫微服平台，支持从零开始创建应用、迁移 Docker 应用、优化现有配置等场景。

## 快速导航

根据你的需求，选择对应的指导：

| 场景 | 说明 | 跳转 |
|------|------|------|
| 首次部署新应用 | 从零开始创建懒猫微服应用 | [快速开始](#快速开始) |
| 迁移 Docker 应用 | 将现有 Docker/Docker-Compose 应用迁移到懒猫微服 | [Docker 迁移](./references/docker-migration.md) |
| 查看配置规范 | 了解 manifest 和 build 配置文件的详细说明 | [配置规范](#配置规范) |
| 查看示例代码 | 查看各种类型应用的完整配置示例 | [示例集](./references/examples.md) |
| 发布到应用商店 | 准备将应用提交到懒猫应用商店 | [商店发布](./references/store-publishing.md) |
| 路由配置 | 配置复杂的 HTTP 路由规则 | [路由详解](./references/route-patterns.md) |

---

## 快速开始

### 第一步：环境准备

确保已安装 `lzc-cli` 命令行工具：

```bash
# 检查版本
lzc-cli version

# 如未安装，请访问 https://developer.lazycat.cloud/lzc-cli.html
```

### 第二步：创建项目

使用脚手架创建新项目：

```bash
# 创建项目
lzc-cli project create myapp

# 按提示选择模板（vue3/react/python/go 等）
```

项目结构：

```
myapp/
├── lzc-build.yml      # 构建配置文件
├── lzc-manifest.yml   # 应用部署配置文件
├── lzc-icon.png       # 应用图标
└── ui/                # 前端代码（或 backend/）
```

### 第三步：本地开发测试

进入开发环境进行开发：

```bash
cd myapp
lzc-cli project devshell

# 在容器内安装依赖并启动
npm install
npm run dev
```

访问应用：在浏览器中打开终端显示的 URL（如 `https://myapp.xxx.heiyu.space`）

### 第四步：构建和部署

开发完成后，构建并安装应用：

```bash
# 构建应用包
lzc-cli project build -o release.lpk

# 安装到微服
lzc-cli app install release.lpk
```

完成！现在你可以在启动器中看到并打开你的应用。

---

## 核心工作流程

懒猫微服应用开发的完整流程：

```
1. 创建项目
   └─ lzc-cli project create <name>

2. 开发阶段
   └─ lzc-cli project devshell
      ├─ 编写代码
      ├─ 测试功能
      └─ 调试路由/环境变量

3. 构建应用
   └─ lzc-cli project build
      ├─ 执行 buildscript（编译/打包）
      ├─ 打包 contentdir 内容
      └─ 生成 .lpk 文件

4. 本地部署
   └─ lzc-cli app install <app.lpk>
      ├─ 拉取 Docker 镜像
      ├─ 创建容器和网络
      └─ 启动应用

5. 商店发布（可选）
   └─ lzc-cli appstore publish <app.lpk>
```

---

## 配置文件生成向导

作为 AI 助手，我会通过交互式问答帮助你生成配置文件。

### 问询策略

我会按以下顺序收集信息：

#### 1. 项目基本信息

- **应用名称**: 给应用起一个易懂的名称
- **Package ID**: 全球唯一标识符（建议格式: `com.yourname.appname`）
- **版本号**: 遵循语义化版本（如 `1.0.0`）
- **应用描述**: 简短说明应用功能

#### 2. 项目类型

- **纯静态网站** (Vite/React/Vue 构建)
- **Node.js 后端应用**
- **Python Web 应用** (Flask/FastAPI/Django)
- **Go 应用**
- **前后端分离应用**
- **带数据库的多服务应用**
- **迁移 Docker 应用**

#### 3. 技术栈细节

根据项目类型询问：

- 前端框架和构建工具
- 后端语言和框架
- 数据库类型（MySQL/PostgreSQL/Redis 等）
- 是否需要文件存储
- 是否需要暴露 TCP/UDP 端口

#### 4. 路由配置

- 静态文件路径
- API 服务端口
- 是否需要多域名支持
- 是否需要 WebSocket

#### 5. 高级特性（可选）

- 是否需要多语言支持
- 是否需要 GPU/USB/KVM 硬件加速
- 是否支持多实例部署
- 是否需要自定义部署参数

### 配置生成示例

基于你的回答，我会生成类似这样的配置：

**lzc-manifest.yml**:
```yaml
lzc-sdk-version: '0.1'
package: com.example.myapp       # 你提供的 Package ID
version: 1.0.0                   # 你提供的版本号
name: My Application             # 你提供的应用名称
description: A cool application  # 你提供的描述

application:
  subdomain: myapp               # 访问域名前缀

  # 路由配置（根据你的技术栈生成）
  routes:
    - /=file:///lzcapp/pkg/content/dist  # 静态前端
    - /api/=http://127.0.0.1:3000/api/   # 后端 API

  # 环境变量（根据需要生成）
  environment:
    - NODE_ENV=production
    - DATABASE_URL=postgresql://user:pass@postgres:5432/db

  # 健康检查
  health_check:
    test_url: http://127.0.0.1:3000/health
    start_period: 30s

# 服务配置（如果有数据库等）
services:
  postgres:
    image: registry.lazycat.cloud/postgres:15
    environment:
      - POSTGRES_PASSWORD={{ stable_secret "db_password" }}
    binds:
      - /lzcapp/var/postgres:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready"]
      interval: 10s
```

**lzc-build.yml**:
```yaml
buildscript: sh build.sh         # 构建脚本
manifest: ./lzc-manifest.yml     # manifest 路径
contentdir: ./dist               # 打包内容目录
pkgout: ./                       # 输出路径
icon: ./lzc-icon.png             # 图标

# 开发环境配置
devshell:
  routes:
    - /=http://127.0.0.1:5173    # 前端开发服务器
    - /api/=http://127.0.0.1:3000 # 后端开发服务器
  dependencies:
    - nodejs
    - npm
  setupscript: |
    export npm_config_registry=https://registry.npmmirror.com
```

---

## 部署命令序列

### 标准部署流程

```bash
# 1. 创建项目（首次）
lzc-cli project create myapp

# 2. 进入开发环境
cd myapp
lzc-cli project devshell

# 3. 在容器内开发和测试
npm install
npm run dev

# 4. 退出容器（Ctrl+D）

# 5. 构建应用包
lzc-cli project build -o release.lpk

# 6. 安装到微服
lzc-cli app install release.lpk

# 7. 查看应用状态
lzc-cli app list

# 8. 查看日志（如有问题）
lzc-cli docker logs -f <container-name>
```

### Docker 应用迁移流程

```bash
# 1. 准备配置文件
# 创建 lzc-manifest.yml 和 lzc-build.yml

# 2. 推送镜像到官方仓库（如需上架商店）
lzc-cli appstore copy-image your-image:tag

# 3. 修改 manifest 中的镜像引用
# image: registry.lazycat.cloud/yourname/your-image:<hash>

# 4. 构建和安装
lzc-cli project build -o app.lpk
lzc-cli app install app.lpk
```

### 应用更新流程

```bash
# 1. 修改代码并更新版本号
# 编辑 lzc-manifest.yml 中的 version 字段

# 2. 重新构建
lzc-cli project build -o app-v2.lpk

# 3. 卸载旧版本
lzc-cli app uninstall com.example.myapp

# 4. 安装新版本
lzc-cli app install app-v2.lpk
```

### 商店发布流程

```bash
# 1. 确保配置了多语言支持（必须）
# 在 manifest 中添加 locales 配置

# 2. 推送镜像到官方仓库
lzc-cli appstore copy-image your-image:tag

# 3. 构建最终版本
lzc-cli project build

# 4. 提交商店审核
lzc-cli appstore publish ./your-app.lpk
```

---

## 最佳实践

### 1. Package ID 命名规范

**格式**: `domain.company.product`

**示例**:
- `cloud.lazycat.app.wordpress` (官方应用)
- `com.example.myapp` (个人应用)
- `org.company.product` (组织应用)

**规则**:
- 必须全球唯一
- 使用反向域名格式
- 只包含小写字母、数字、点号
- 不要使用中文或特殊字符

### 2. 版本号管理

遵循[语义化版本](https://semver.org/)规范：

- **主版本号 (X)**: 不兼容的 API 修改
- **次版本号 (Y)**: 向下兼容的功能性新增
- **修订号 (Z)**: 向下兼容的问题修正

示例: `1.2.3`

### 3. 路由配置原则

**优先级**（从高到低）:
1. 精确路径匹配
2. 长路径前缀匹配
3. 短路径前缀匹配

**推荐写法**:
```yaml
routes:
  # 精确路径在前
  - /api/v2/=http://127.0.0.1:8080/
  - /api/=http://127.0.0.1:3000/
  # 通配符在后
  - /=file:///lzcapp/pkg/content/
```

### 4. 存储路径规范

**永久存储** (`/lzcapp/var`):
- 数据库文件
- 用户上传的文件
- 应用配置文件
- 重要日志

**缓存存储** (`/lzcapp/cache`):
- 临时文件
- 可重新生成的缓存
- 非关键日志

**示例**:
```yaml
binds:
  - /lzcapp/var/data:/app/data           # 重要数据
  - /lzcapp/var/config:/app/config       # 配置文件
  - /lzcapp/cache/tmp:/app/tmp           # 临时文件
  - /lzcapp/cache/thumbnails:/app/cache  # 可重建的缓存
```

### 5. 健康检查配置

**Application (app 容器)**:
```yaml
health_check:
  test_url: http://127.0.0.1:3000/health  # 推荐使用 test_url
  timeout: 5s
  start_period: 30s  # 给应用足够的启动时间
```

**Services**:
```yaml
healthcheck:
  test: ["CMD", "pg_isready"]  # 使用服务自带的健康检查命令
  interval: 10s
  timeout: 5s
  retries: 3
  start_period: 30s
```

### 6. 多语言本地化要求

**商店应用必须配置至少两种语言**（中文 + 英文）:

```yaml
locales:
  zh_CN:
    name: "我的应用"
    description: "这是一个很棒的应用"
  en:
    name: "My App"
    description: "This is an awesome app"
```

### 7. 安全注意事项

**密码管理**:
```yaml
# 不要硬编码密码
environment:
  - DB_PASSWORD=mysecret123  # ❌ 错误

# 使用 stable_secret 生成稳定密码
environment:
  - DB_PASSWORD={{ stable_secret "db_password" }}  # ✅ 正确
```

**网络权限**:
```yaml
# 除非必要，不要使用 host 网络模式
network_mode: host  # ⚠️ 慎用

# 除非必要，不要请求 NET_ADMIN 权限
netadmin: true  # ⚠️ 慎用
```

**端口暴露**:
```yaml
# 不要随意暴露 80/443 端口
ingress:
  - yes_i_want_80_443: true  # ⚠️ 会绕过系统鉴权
```

### 8. 镜像选择建议

**优先级**:
1. 官方镜像仓库: `registry.lazycat.cloud/official-image`
2. 通过 `copy-image` 推送的镜像: `registry.lazycat.cloud/yourname/image`
3. Docker Hub: `docker.io/image`（可能网络不稳定）

**最小化原则**:
- 优先使用 Alpine 版本（体积小）
- 避免使用 `latest` 标签
- 使用具体版本号或 hash 标签

### 9. 资源限制建议

```yaml
services:
  myservice:
    mem_limit: 1G        # 设置合理的内存限制
    cpus: 1.0            # CPU 核心数限制
    shm_size: 128M       # 共享内存大小（某些应用需要）
```

### 10. 开发环境配置

```yaml
devshell:
  # 配置国内镜像源（提升速度）
  setupscript: |
    export npm_config_registry=https://registry.npmmirror.com
    export PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
    export GOPROXY=https://goproxy.cn
```

---

## 常见问题

### 1. 镜像拉取失败

**问题**: `docker pull` 失败或超时

**解决方案**:
- 使用 `lzc-cli appstore copy-image` 推送到官方仓库
- 或在 manifest 中使用国内镜像源
- 检查网络连接

### 2. 路由 404 错误

**问题**: 访问应用返回 404

**常见原因**:
- 路由路径配置错误
- 服务未启动或端口不正确
- 健康检查失败导致服务未就绪

**调试方法**:
```bash
# 查看容器日志
lzc-cli docker logs -f <container-name>

# 进入容器检查
lzc-cli docker exec -it <container-name> sh

# 检查端口监听
netstat -tlnp | grep <port>
```

### 3. 数据丢失问题

**问题**: 重启后数据丢失

**原因**: 数据未存储在 `/lzcapp/var` 或 `/lzcapp/cache`

**解决方案**:
```yaml
binds:
  # 确保数据库等重要数据绑定到 /lzcapp/var
  - /lzcapp/var/mysql:/var/lib/mysql
  - /lzcapp/var/uploads:/app/uploads
```

### 4. 端口冲突

**问题**: 应用无法启动，提示端口被占用

**原因**: 多个服务使用相同端口

**解决方案**:
- 检查 `routes` 中的端口号
- 确保每个服务使用不同端口
- 使用 `lzc-cli docker ps` 查看端口占用

### 5. 环境变量未生效

**问题**: 容器内无法读取环境变量

**检查清单**:
```yaml
# 1. 确保在正确位置定义
application:
  environment:  # ✅ application 的环境变量
    - KEY=VALUE

services:
  myservice:
    environment:  # ✅ service 的环境变量
      - KEY=VALUE

# 2. 格式正确（KEY=VALUE）
environment:
  - NODE_ENV=production  # ✅ 正确
  - NODE_ENV: production # ❌ 错误（这是 YAML 对象）

# 3. 在容器内验证
# lzc-cli docker exec -it <container> sh
# echo $KEY
```

### 6. 健康检查一直失败

**问题**: 应用无法启动，健康检查持续失败

**排查步骤**:

1. **增加启动等待时间**:
```yaml
health_check:
  start_period: 60s  # 给应用更多启动时间
```

2. **检查健康检查 URL**:
```yaml
health_check:
  test_url: http://127.0.0.1:3000/health  # 确保路径和端口正确
```

3. **临时禁用健康检查进行调试**:
```yaml
health_check:
  disable: true  # 仅用于调试
```

### 7. 构建时依赖安装失败

**问题**: `npm install` 或 `pip install` 失败

**解决方案**:
```yaml
devshell:
  setupscript: |
    # 使用国内镜像源
    export npm_config_registry=https://registry.npmmirror.com
    export PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
```

### 8. 商店审核被拒

**常见原因**:
- 缺少多语言配置（必须至少中英文）
- 镜像未推送到官方仓库
- 应用无法安装或启动
- 缺少应用图标或描述

**解决方案**: 参考 [商店发布指南](./references/store-publishing.md)

---

## 配置规范

### 主要配置文件

| 文件 | 用途 | 详细文档 |
|------|------|----------|
| `lzc-manifest.yml` | 应用部署配置 | [Manifest 规范](./references/manifest-spec.md) |
| `lzc-build.yml` | 构建配置 | [Build 规范](./references/build-spec.md) |
| `lzc-deploy-params.yml` | 部署参数（可选） | [部署参数](./references/deploy-params.md) |

### 快速参考

**lzc-manifest.yml 核心字段**:
```yaml
package: com.example.app    # 唯一标识
version: 1.0.0              # 版本号
name: My App                # 应用名称

application:                # 应用配置
  subdomain: app            # 访问域名
  routes: []                # 路由规则
  environment: []           # 环境变量
  health_check: {}          # 健康检查

services: {}                # 额外服务（数据库等）
locales: {}                 # 多语言配置
```

**lzc-build.yml 核心字段**:
```yaml
buildscript: sh build.sh    # 构建脚本
manifest: ./lzc-manifest.yml # manifest 路径
contentdir: ./dist          # 打包内容
pkgout: ./                  # 输出路径
icon: ./lzc-icon.png        # 图标

devshell:                   # 开发环境
  routes: []                # 开发路由
  dependencies: []          # 依赖包
```

---

## 参考资料

### 官方文档

- [开发者中心](https://developer.lazycat.cloud)
- [应用商店](https://appstore.lazycat.cloud)
- [社区论坛](https://lazycat.cloud/playground)

### 本 Skill 文档

- [Manifest 完整规范](./references/manifest-spec.md)
- [Build 配置规范](./references/build-spec.md)
- [路由配置详解](./references/route-patterns.md)
- [Docker 迁移指南](./references/docker-migration.md)
- [部署参数配置](./references/deploy-params.md)
- [商店发布流程](./references/store-publishing.md)
- [完整示例集](./references/examples.md)

### 开源资源

- [官方移植仓库](https://gitee.com/lazycatcloud/appdb)
- [社区转换工具](https://www.npmjs.com/package/docker2lzc)

---

## 如何使用本 Skill

作为 AI 助手，我可以帮助你：

1. **生成配置文件**: 告诉我你的项目类型和需求，我会生成完整的配置文件
2. **迁移 Docker 应用**: 提供你的 `docker-compose.yml` 或 Docker 命令，我会帮你转换
3. **优化现有配置**: 提供你的 manifest 文件，我会提出优化建议
4. **解答问题**: 关于路由、存储、健康检查等任何配置问题
5. **准备商店发布**: 帮助你检查和完善商店上架所需的配置

**开始对话示例**:
- "我想部署一个 Vue 项目到懒猫微服"
- "帮我把这个 docker-compose.yml 转换成懒猫配置"
- "我的应用路由配置不工作，帮我看看"
- "如何配置 PostgreSQL 数据库？"
- "准备上架应用商店，需要做什么？"

随时向我提问！

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashuihui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
