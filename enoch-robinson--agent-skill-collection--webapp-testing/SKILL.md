---
name: webapp-testing
description: Web应用测试工具包。使用 Playwright 进行前端自动化测试、UI 调试、截图捕获、浏览器日志查看。当需要测试本地 Web 应用、验证前端功能、调试 UI 行为时使用此技能。 Use when this capability is needed.
metadata:
  author: enoch-robinson
---

# Web Application Testing

使用 Playwright 测试本地 Web 应用。

## 决策树

```
任务 → 是静态 HTML？
├─ 是 → 直接读取 HTML 识别选择器 → 编写 Playwright 脚本
└─ 否（动态应用）→ 服务器已运行？
    ├─ 否 → 先启动服务器
    └─ 是 → 侦察后操作：
        1. 导航并等待 networkidle
        2. 截图或检查 DOM
        3. 识别选择器
        4. 执行操作
```

## 基础用法

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    page = browser.new_page()
    
    page.goto('http://localhost:5173')
    page.wait_for_load_state('networkidle')  # 关键：等待 JS 执行
    
    # 操作
    page.click('button#submit')
    page.fill('input[name="email"]', 'test@example.com')
    
    # 截图
    page.screenshot(path='screenshot.png', full_page=True)
    
    browser.close()
```

## 启动服务器后测试

```bash
# 单服务器
npm run dev &
sleep 3
python test_script.py

# 或使用脚本管理
python with_server.py --server "npm run dev" --port 5173-- python test.py
```

## 常用操作

### 元素定位
```python
# 文本
page.click('text=登录')

# CSS 选择器
page.click('#submit-btn')
page.click('.form-input')

# 角色
page.click('role=button[name="提交"]')

# 组合
page.locator('form').locator('button').click()
```

### 等待策略
```python
# 等待元素出现
page.wait_for_selector('#content')

# 等待网络空闲
page.wait_for_load_state('networkidle')

# 固定等待（不推荐，但有时必要）
page.wait_for_timeout(1000)
```

### 表单操作
```python
page.fill('input[name="username"]', 'admin')
page.fill('input[name="password"]', '123456')
page.click('button[type="submit"]')
```

### 截图调试
```python
# 全页截图
page.screenshot(path='full.png', full_page=True)

# 元素截图
page.locator('#header').screenshot(path='header.png')
```

### 获取内容
```python
# 页面 HTML
html = page.content()

# 元素文本
text = page.locator('#result').text_content()

# 所有匹配元素
buttons = page.locator('button').all()
```

## 常见陷阱

|❌ 错误 | ✅ 正确 |
|--------|--------|
| 不等待直接操作 | 先`wait_for_load_state('networkidle')` |
| 硬编码等待时间 | 使用 `wait_for_selector` |
| 忘记关闭浏览器 | 使用 `with` 语句或 `browser.close()` |

## 最佳实践

- 始终使用 `headless=True` 运行
- 动态应用必须等待 `networkidle`
- 使用描述性选择器：`text=`、`role=`、ID
- 添加适当等待避免竞态条件
- 测试完成后关闭浏览器

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enoch-robinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
