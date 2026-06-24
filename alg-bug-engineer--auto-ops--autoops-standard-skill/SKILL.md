---
name: autoops-standard-skill
description: AutoOps 项目标准技能（完整版）。包含完整 SOP、质量门禁、MCP Playwright 测试、README 功能验证与 curl 测试流程。 Use when this capability is needed.
metadata:
  author: alg-bug-engineer
---

# AutoOps 标准技能（完整版）

本技能是该项目的"标准化总技能入口"，提供从代码到部署再到验证的完整 SOP 流程。

## 目标与承诺

- **完整性**：原始规范文档保存在 references 中
- **一致性**：提供校验脚本验证规范未漂移
- **可执行**：完整 SOP（audit → build → runtime → test → integration）
- **可验证**：包含 curl 测试、MCP Playwright 浏览器测试、README 功能覆盖检查

---

## 参考材料

按以下顺序加载：

| 顺序 | 文件 | 用途 |
|------|------|------|
| 1 | `references/source-QUICKREF.zh.md` | 快速检查清单 |
| 2 | `references/source-SKILL.zh.md` | 中文完整规范（设计系统 + 流程） |
| 3 | `references/source-Skills_en.md` | 英文规范（contract + gates） |
| 4 | `references/combined-full.md` | 完整合并版（不能遗漏时使用） |

---

## 统一 SOP（总览）

```
┌─────────────────────────────────────────────────────────────────┐
│                    AutoOps 标准化 SOP                            │
├─────────────────────────────────────────────────────────────────┤
│ 1. 校验规范一致性 (validate_skill.sh)                            │
│ 2. 项目审计 (Audit Gate)                                         │
│ 3. 构建容器 (Build Gate)                                         │
│ 4. 启动服务 (Runtime Gate)                                       │
│ 5. curl API 测试 (API Gate)                                      │
│ 6. MCP Playwright 浏览器测试 (UI Gate)                           │
│ 7. README 功能覆盖验证 (Feature Gate)                            │
│ 8. 安全与可观测性检查 (Security/Observability Gate)              │
│ 9. 集成到 Homepage (Integration Gate)                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## 质量门禁

| 门禁 | 检查项 | 通过条件 |
|------|--------|----------|
| **Audit** | 可构建/可运行/风险可控 | 无 GPU 强依赖，无仅 GUI 模式 |
| **Build** | 构建可重复、无交互 | `docker build` 无报错 |
| **Runtime** | 绑定 `$PORT`，监听 `0.0.0.0` | 容器正常启动 |
| **API** | 健康检查与核心 API 可用 | `/health` 返回 200 |
| **UI** | 页面可访问，功能可操作 | Playwright 测试通过 |
| **Feature** | UI 功能与 README 一致 | 功能覆盖率 100% |
| **Security** | 无硬编码密钥，支持 env 配置 | 无敏感信息泄露 |
| **Observability** | 日志可读、结构化 | 无 fatal 错误 |
| **Integration** | 能被 Homepage 发现和管理 | 工具卡片正常显示 |

---

## 阶段 1：校验规范一致性

```bash
bash skills/autoops-standard-skill/scripts/validate_skill.sh
```

---

## 阶段 2：项目审计

输出 `audit_report.yaml`：

```yaml
audit_result:
  status: PASS|WARN|FAIL
  tool_slug: "<tool_slug>"
  detected:
    language: "Python|Node|Go"
    mode: "cli|web|library"
  requirements:
    network: true
    browser: false
    gpu: false
  risks: []
```

---

## 阶段 3：构建 Docker 容器

```bash
# 构建镜像
./scripts/deploy-<tool>-web.sh build

# 验证镜像存在
docker images | grep "<tool>-web"
```

---

## 阶段 4：curl API 测试（构建后必做）

### 4.1 启动容器

```bash
./scripts/deploy-<tool>-web.sh run
# 等待 5 秒让服务启动
sleep 5
```

### 4.2 健康检查测试

```bash
# 基础健康检查
curl -fsS http://localhost:<PORT>/health
# 期望输出: {"status": "healthy"} 或 {"status": "ok"}

# 带超时的健康检查
curl -fsS --max-time 10 http://localhost:<PORT>/health || echo "FAIL: health check failed"
```

### 4.3 API 端点测试

```bash
# 1. 工具信息 API
curl -fsS http://localhost:<PORT>/api/info | jq .
# 验证: 返回 JSON 包含 name, version, description

# 2. 任务启动 API
TASK_RESPONSE=$(curl -fsS -X POST http://localhost:<PORT>/api/process \
  -H 'Content-Type: application/json' \
  -d '{"input": "test", "options": {}}')
TASK_ID=$(echo "$TASK_RESPONSE" | jq -r '.task_id')
echo "Task ID: $TASK_ID"

# 3. 任务状态查询
curl -fsS http://localhost:<PORT>/api/tasks/${TASK_ID} | jq .

# 4. 获取语音/选项列表 (如适用)
curl -fsS http://localhost:<PORT>/api/voices | jq . 2>/dev/null || true
```

### 4.4 curl 测试脚本模板

```bash
#!/bin/bash
# test-api.sh - API 自动化测试脚本

set -e

TOOL_NAME="<tool>"
PORT="${PORT:-5400}"
BASE_URL="http://localhost:${PORT}"

log_pass() { echo -e "\033[32m[PASS]\033[0m $1"; }
log_fail() { echo -e "\033[31m[FAIL]\033[0m $1"; exit 1; }

echo "=== Testing ${TOOL_NAME} API ==="

# Test 1: Health check
if curl -fsS --max-time 5 "${BASE_URL}/health" > /dev/null 2>&1; then
    log_pass "Health check"
else
    log_fail "Health check"
fi

# Test 2: API info
INFO=$(curl -fsS "${BASE_URL}/api/info" 2>/dev/null)
if echo "$INFO" | jq -e '.name' > /dev/null 2>&1; then
    log_pass "API info - name: $(echo $INFO | jq -r '.name')"
else
    log_fail "API info"
fi

# Test 3: Process endpoint exists
if curl -fsS -X POST "${BASE_URL}/api/process" \
     -H 'Content-Type: application/json' \
     -d '{}' 2>&1 | grep -q "task_id\|error"; then
    log_pass "Process endpoint responds"
else
    log_fail "Process endpoint"
fi

echo "=== All API tests passed ==="
```

---

## 阶段 5：MCP Playwright 浏览器测试

使用 MCP Playwright 进行自动化浏览器测试，验证 Web UI 功能。

### 5.1 Playwright 测试场景

| 测试类型 | 检查内容 | 期望结果 |
|----------|----------|----------|
| 页面加载 | 首页是否正常渲染 | 无 JS 错误，DOM 加载完成 |
| 表单交互 | 输入框、按钮是否可用 | 可输入、可点击 |
| API 调用 | 提交表单后的响应 | 显示任务状态或结果 |
| 下载功能 | 文件下载链接 | 可成功下载文件 |
| 错误处理 | 无效输入的提示 | 显示友好错误信息 |

### 5.2 MCP Playwright 测试流程

```
使用 MCP Playwright 执行以下测试:

1. 导航到 http://localhost:<PORT>
2. 等待页面加载完成 (检查关键元素存在)
3. 截图保存为 test-screenshots/01-homepage.png
4. 定位表单输入框，输入测试内容
5. 点击提交按钮
6. 等待任务完成或结果显示
7. 截图保存为 test-screenshots/02-result.png
8. 验证结果区域内容
9. 如有下载按钮，验证下载链接有效
```

### 5.3 Playwright 测试检查清单

- [ ] 页面在 5 秒内加载完成
- [ ] 无 console.error 错误
- [ ] 主要交互元素可见且可点击
- [ ] 表单提交后有响应反馈
- [ ] 任务状态实时更新 (WebSocket)
- [ ] 结果可下载或可查看
- [ ] 移动端视口下布局正常

### 5.4 Playwright 测试报告模板

```yaml
playwright_test_report:
  tool: "<tool_slug>"
  url: "http://localhost:<PORT>"
  timestamp: "2026-01-28T10:00:00Z"
  tests:
    - name: "Page Load"
      status: PASS|FAIL
      duration_ms: 1234
      screenshot: "test-screenshots/01-homepage.png"
    - name: "Form Submission"
      status: PASS|FAIL
      duration_ms: 2345
      screenshot: "test-screenshots/02-submit.png"
    - name: "Result Display"
      status: PASS|FAIL
      duration_ms: 3456
      screenshot: "test-screenshots/03-result.png"
  console_errors: []
  overall: PASS|FAIL
```

---

## 阶段 6：README 功能覆盖验证

验证 UI 实现的功能是否与 README/tool.yaml 文档一致。

### 6.1 功能对照矩阵

对于每个工具，创建功能对照表：

| 文档描述的功能 | UI 是否实现 | 实现位置 | 备注 |
|----------------|-------------|----------|------|
| 文本转语音 | ✅ | 主页表单 | - |
| 多语言支持 | ✅ | 语音下拉菜单 | - |
| 语速调节 | ✅ | 滑块控件 | - |
| SRT 字幕生成 | ✅ | 复选框选项 | - |
| 文件下载 | ✅ | 结果区下载按钮 | - |

### 6.2 README 功能提取流程

```bash
# 从 docs/<tool>-web-ui-guide.md 提取功能列表
grep -E "^- \*\*|^### " docs/<tool>-web-ui-guide.md

# 从 tool.yaml 提取功能描述
yq '.long_description' tools/<tool>-main/tool.yaml
yq '.api_endpoints[].description' tools/<tool>-main/tool.yaml
```

### 6.3 功能验证清单生成

```bash
#!/bin/bash
# generate-feature-checklist.sh

TOOL_NAME="$1"
TOOL_YAML="tools/${TOOL_NAME}-main/tool.yaml"
GUIDE_MD="docs/${TOOL_NAME}-web-ui-guide.md"

echo "=== ${TOOL_NAME} 功能验证清单 ==="
echo ""

echo "## tool.yaml 声明的功能:"
if [ -f "$TOOL_YAML" ]; then
    yq '.long_description' "$TOOL_YAML" 2>/dev/null | grep -E "^  - " || echo "(无)"
fi

echo ""
echo "## API 端点:"
if [ -f "$TOOL_YAML" ]; then
    yq '.api_endpoints[].endpoint + " - " + .api_endpoints[].description' "$TOOL_YAML" 2>/dev/null || echo "(无)"
fi

echo ""
echo "## 使用指南中的功能:"
if [ -f "$GUIDE_MD" ]; then
    grep -E "^- \*\*" "$GUIDE_MD" || echo "(无)"
fi
```

### 6.4 功能遗漏报告模板

```yaml
feature_coverage_report:
  tool: "<tool_slug>"
  source_docs:
    - "docs/<tool>-web-ui-guide.md"
    - "tools/<tool>-main/tool.yaml"
  total_features: 10
  implemented: 9
  missing: 1
  coverage: "90%"
  details:
    implemented:
      - "文本转语音"
      - "多语言支持"
      - "语速调节"
    missing:
      - feature: "批量处理"
        documented_in: "tool.yaml long_description"
        priority: "low"
        notes: "可在后续版本添加"
```

---

## 阶段 7：安全与可观测性检查

### 7.1 安全检查

```bash
# 检查硬编码密钥
grep -rn "password\|secret\|api_key\|token" tools/<tool>-main/<tool>-web/ --include="*.py" | grep -v "environ\|getenv"

# 检查是否支持环境变量配置
grep -n "os.environ\|os.getenv" tools/<tool>-main/<tool>-web/app.py
```

### 7.2 日志检查

```bash
# 检查容器日志无 fatal 错误
docker logs <tool>-web-container 2>&1 | tail -n 200 | \
  grep -E "Traceback|panic:|Segmentation fault|FATAL|CRITICAL" && \
  echo "FAIL: Fatal errors found" || echo "PASS: No fatal errors"

# 检查服务启动日志
docker logs <tool>-web-container 2>&1 | \
  grep -E "Starting|Listening|Server started|port" | head -5
```

---

## 阶段 8：集成验证

### 8.1 Homepage 集成检查

```bash
# 确认 tool.yaml 存在且格式正确
cat tools/<tool>-main/tool.yaml | yq '.container_name'

# 确认容器名符合规范
docker ps --format "{{.Names}}" | grep "<tool>-web-container"

# 通过 Homepage API 检查工具是否被发现
curl -fsS http://localhost:8000/api/tools | jq '.[] | select(.name | contains("<tool>"))'
```

### 8.2 集成测试清单

- [ ] `tool.yaml` 的 `container_name` 与实际容器名一致
- [ ] 容器名以 `-container` 结尾
- [ ] Homepage 可列出该工具
- [ ] 可通过 Homepage 启动/停止该工具
- [ ] 工具卡片显示正确的状态

---

## 完整测试脚本

```bash
#!/bin/bash
# full-test.sh - 完整测试流程

set -e

TOOL_NAME="${1:-edge-tts}"
PORT="${2:-5500}"

echo "========================================="
echo "AutoOps 工具完整测试: ${TOOL_NAME}"
echo "========================================="

# 1. 构建
echo "[1/6] Building..."
./scripts/deploy-${TOOL_NAME}-web.sh build

# 2. 启动
echo "[2/6] Starting container..."
./scripts/deploy-${TOOL_NAME}-web.sh run
sleep 5

# 3. curl API 测试
echo "[3/6] API tests..."
curl -fsS http://localhost:${PORT}/health || exit 1
curl -fsS http://localhost:${PORT}/api/info | jq . || exit 1

# 4. 日志检查
echo "[4/6] Log check..."
docker logs ${TOOL_NAME}-web-container 2>&1 | tail -50 | \
  grep -E "Traceback|FATAL" && exit 1 || true

# 5. MCP Playwright 测试 (需要 MCP 环境)
echo "[5/6] Browser test..."
echo "  -> 请使用 MCP Playwright 执行浏览器测试"
echo "  -> 目标 URL: http://localhost:${PORT}"

# 6. 功能覆盖检查
echo "[6/6] Feature coverage..."
echo "  -> 请对照 docs/${TOOL_NAME}-web-ui-guide.md 检查功能"

echo "========================================="
echo "测试完成！请手动验证 Playwright 和功能覆盖"
echo "========================================="
```

---

## test_report.json 完整模板

```json
{
  "tool_slug": "<tool_slug>",
  "timestamp": "2026-01-28T10:00:00Z",
  "gates": {
    "audit": {"status": "PASS", "notes": ""},
    "build": {"status": "PASS", "image": "<tool>-web:latest"},
    "runtime": {"status": "PASS", "container": "<tool>-web-container", "port": 5500},
    "api": {
      "status": "PASS",
      "tests": [
        {"endpoint": "/health", "method": "GET", "status": 200},
        {"endpoint": "/api/info", "method": "GET", "status": 200},
        {"endpoint": "/api/process", "method": "POST", "status": 200}
      ]
    },
    "ui": {
      "status": "PASS",
      "playwright_tests": [
        {"name": "page_load", "status": "PASS"},
        {"name": "form_submit", "status": "PASS"},
        {"name": "result_display", "status": "PASS"}
      ],
      "screenshots": ["01-homepage.png", "02-result.png"]
    },
    "feature": {
      "status": "PASS",
      "coverage": "100%",
      "total": 10,
      "implemented": 10,
      "missing": []
    },
    "security": {"status": "PASS", "hardcoded_secrets": false},
    "observability": {"status": "PASS", "fatal_errors": []},
    "integration": {"status": "PASS", "homepage_visible": true}
  },
  "overall": "PASS"
}
```

---

## 快速命令参考

```bash
# 校验规范
bash skills/autoops-standard-skill/scripts/validate_skill.sh

# 构建并测试
./scripts/deploy-<tool>-web.sh build
./scripts/deploy-<tool>-web.sh run
curl http://localhost:<PORT>/health

# 查看日志
docker logs <tool>-web-container

# 停止服务
./scripts/deploy-<tool>-web.sh stop
```

---

## 注意事项

- 本技能不覆盖下载型插件/安装包的 SOP，请参见：
  - `skills/plugin-distribution-standardizer/SKILL.md`
- 本技能聚焦 deployable/service standardization
- MCP Playwright 测试需要配置 Playwright MCP 环境
- 功能覆盖验证依赖 `docs/<tool>-web-ui-guide.md` 文档

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alg-bug-engineer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
