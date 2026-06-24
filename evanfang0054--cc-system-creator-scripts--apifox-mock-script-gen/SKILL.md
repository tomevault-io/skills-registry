---
name: apifox-mock-script-gen
description: | Use when this capability is needed.
metadata:
  author: evanfang0054
---

# Apifox Mock 脚本生成器

## 核心原则

> ⚠️ **无文档不生成！必须先获取 API 文档，才能编写 Mock 脚本。**

## 反模式警示（禁止行为）

### 反模式1：凭经验猜测数据结构
```
❌ 听到"营业时间"就认为是 object {monday: "5-18"}
✅ 必须先查看文档，确认是 array 还是 object
```

### 反模式2：跳过文档获取步骤
```
❌ 用户描述需求后直接生成代码
✅ 必须先调用 apifox_get_api_detail 获取文档
```

### 反模式3：忽略嵌套类型
```
❌ 只检查顶层字段 $.data.businessHours 的类型
✅ 必须检查嵌套字段 $.data.businessHours[0].day 的类型
```

## 执行流程（强制顺序）

### 第一阶段：信息收集 🔴 [必须先完成]

```markdown
1. 🔴 强制：调用 mcp__apifox-api-docs-mcp__apifox_get_api_list
   └─ 如果失败，停止并报错："无法获取接口列表"

2. 🔴 强制：匹配目标接口并获取 apiId
   └─ 如果找不到，停止并报错："未找到目标接口"

3. 🔴 强制：调用 mcp__apifox-api-docs-mcp__apifox_get_api_detail
   └─ 如果失败，停止并报错："无法获取接口详情"

4. 🔴 强制：提取类型定义并创建映射表
   └─ 遍历所有字段，记录路径和类型
```

### 第二阶段：需求确认（可选）

在获取文档后，向用户确认：
- [ ] 是否需要分页支持？
- [ ] 是否需要 Token 验证？
- [ ] 是否需要模拟网络延迟？
- [ ] 是否需要异常场景（404、500 等）？
- [ ] 是否需要参数校验逻辑？

### 第三阶段：脚本生成（基于文档）

```markdown
1. 参照类型映射表生成 Mock 数据
2. 根据需求选择代码模式（见 references/code-patterns.md）
3. 为每个字段添加类型注释
```

### 第四阶段：质量验证（逐字段检查）

```markdown
✓ 步骤1：验证 API 调用方式（使用 fox. 前缀）
✓ 步骤2：验证函数调用（handleRequest() 被调用）
✓ 步骤3：🔴 逐字段验证类型（对照类型映射表）
✓ 步骤4：验证嵌套类型（array 元素、object 字段）
✓ 步骤5：验证必填字段（有默认值）
✓ 步骤6：验证注释完整性
```

## 类型映射表创建流程

从 API 文档提取类型后，创建类型映射表：

```javascript
// 从 API 文档提取的类型定义
var typeMapping = {
  // 基本类型
  '$.data.code': 'integer',
  '$.data.msg': 'string',
  '$..data.id': 'integer',

  // 对象类型
  '$.data.description': 'object',
  '$.data.description.title': 'string',

  // 数组类型
  '$.data.images': 'array',
  '$.data.images[0]': 'string',

  // 嵌套数组对象
  '$.data.businessHours': 'array',
  '$.data.businessHours[0]': 'object',
  '$.data.businessHours[0].day': 'integer',
  '$.data.businessHours[0].open': 'string'
};
```

## 数据类型严格匹配

| 文档类型 | JavaScript 类型 | Mock 示例 |
|---------|----------------|----------|
| `string` | String | `"测试文本"` |
| `integer` | Number (整数) | `123` |
| `number` | Number (浮点) | `123.45` |
| `boolean` | Boolean | `true` |
| `array` | Array | `[1, 2, 3]` |
| `object` | Object | `{ key: 'value' }` |

## 硬约束（必须遵循）

### 1. API 调用规范
```javascript
// ✅ 正确：使用 fox. API
var responseJson = fox.mockResponse.json();
fox.mockResponse.setBody(responseJson);

// ❌ 错误：使用不存在的 API
response.json(data);
res.send(data);
```

### 2. 参数获取策略

| HTTP 方法 | 参数位置 | 正确的 API |
|----------|---------|-----------|
| GET | Query 参数 | `fox.mockRequest.getParam(key)` |
| POST | JSON Body | `fox.mockRequest.body.key` |
| ALL | Headers | `fox.mockRequest.headers.get(key)` |
| ALL | Cookies | `fox.mockRequest.cookies.get(key)` |

### 3. 函数调用要求
```javascript
// ✅ 正确：定义并手动调用
var handleRequest = function() {
  // 逻辑代码
};
handleRequest();

// ❌ 错误：使用参数化函数（不会被执行）
function handleMockRequest(req, res) {
  // 逻辑代码
}
```

## 标准模板

### 基础结构
```javascript
var MockJs = require('mockjs');

var handleRequest = function() {
  var responseJson = fox.mockResponse.json();

  // GET 请求：获取 Query 参数
  var page = fox.mockRequest.getParam('page') || '1';

  // POST 请求：获取 JSON Body
  var body = fox.mockRequest.body || {};

  responseJson.code = 0;
  responseJson.msg = 'success';
  responseJson.data = {
    page: page,
    moduleId: body.module || 1
  };

  fox.mockResponse.setBody(responseJson);
  fox.mockResponse.setDelay(300);
};

handleRequest();
```

## 质量检查清单（生成前必查）

### 阶段0：文档完整性检查 🔴 [前置条件]
- [ ] 已成功调用 `apifox_get_api_list` 并获取接口列表
- [ ] 已成功调用 `apifox_get_api_detail` 并获取完整文档
- [ ] 已从文档中提取所有字段的类型定义
- [ ] 已创建类型映射表（字段路径 → 类型）
- [ ] 如果以上任何一项未完成，**停止生成并报错**

### 阶段1：脚本规范检查
- [ ] 使用 `fox.mockResponse.json()` 获取响应对象
- [ ] 使用 `fox.mockResponse.setBody()` 设置响应
- [ ] 定义 `handleRequest()` 函数并在末尾调用
- [ ] 不使用 `(req, res)` 参数模式
- [ ] 所有 API 调用都使用 `fox.` 前缀

### 阶段2：数据类型检查 🔴 [核心检查]
- [ ] 顶层字段类型与文档一致
- [ ] 嵌套对象字段类型与文档一致
- [ ] 数组元素类型与文档一致
- [ ] 深层嵌套类型正确（如 `$.data.items[0].user.id`）
- [ ] 特殊类型正确（integer 用数字而非字符串）

### 阶段3：功能完整性检查
- [ ] 根据需求添加了分页逻辑
- [ ] 根据需求添加了 Token 验证
- [ ] 根据需求添加了异常场景
- [ ] 根据需求添加了延迟模拟
- [ ] 代码包含必要的注释说明

## 输出格式要求

```javascript
// 1. 引入依赖（如需要）
var MockJs = require('mockjs');

// 2. 主处理函数
var handleRequest = function() {
  // 2.1 获取响应对象
  var responseJson = fox.mockResponse.json();

  // 2.2 获取请求参数（根据 HTTP 方法选择正确方式）
  var param = fox.mockRequest.getParam('key');  // GET
  // var body = fox.mockRequest.body.key;       // POST

  // 2.3 业务逻辑处理（分页、Token、异常等）

  // 2.4 设置响应数据
  responseJson.code = 0;
  responseJson.msg = 'success';
  responseJson.data = { /* Mock 数据 */ };

  // 2.5 返回响应
  fox.mockResponse.setBody(responseJson);
  fox.mockResponse.setDelay(500);  // 可选
};

// 3. 手动调用函数
handleRequest();
```

## 参考资源

### 核心文档
- **代码模式库**：`references/code-patterns.md` - 分页、Token、异常等常见场景的代码模式
- **错误案例库**：`references/error-cases.md` - 常见错误及解决方案
- **API 参考**：`references/api-reference.md` - 完整的 API 文档
- **Mock 脚本指南**：`references/mock_script_guide.md` - Apifox 官方文档

### MCP 工具
- `mcp__apifox-api-docs-mcp__apifox_get_api_list` - 获取接口列表
- `mcp__apifox-api-docs-mcp__apifox_get_api_detail` - 获取接口详情
- `mcp__apifox-api-docs-mcp__apifox_health_check` - 健康检查

### 使用场景指南

**何时读取 code-patterns.md？**
- 需要实现分页逻辑时
- 需要添加 Token 验证时
- 需要模拟异常场景时
- 需要使用 Mock.js 生成随机数据时

**何时读取 error-cases.md？**
- 脚本不生效时
- 获取不到参数时
- 出现类型错误时
- 遇到语法错误时

**何时读取 api-reference.md？**
- 不确定使用哪个 API 时
- 需要查询 API 参数时
- 需要了解数据类型映射时
- 需要查看完整示例时

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/evanfang0054) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
