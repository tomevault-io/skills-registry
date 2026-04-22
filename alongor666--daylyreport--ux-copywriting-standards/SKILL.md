---
name: ux-copywriting-standards
description: Write user-facing copy for vehicle insurance platform UI. Use when writing error messages, button labels, tooltips, success messages, or formatting numbers. Mentions "write message", "button text", "error copy", "toast message", or "format number". Use when this capability is needed.
metadata:
  author: alongor666
---

# UX Copywriting Standards

Professional, concise, and friendly copy guidelines for user interface text.

## When to Activate

Use this skill when the user:
- Asks "write an error message" or "what should the button say?"
- Mentions "toast message", "success text", or "warning copy"
- Needs to "format a number" or "write tooltip text"
- Wants to "improve this message" or "make it more user-friendly"

## Tone Principles

### 1. Professional (专业)
- State facts clearly
- Avoid casual expressions
- Use industry terminology correctly

✅ Good: "数据加载完成，共加载 5,123 条记录"
❌ Bad: "哇！数据超多的！"

### 2. Concise (简洁)
- Use 3-10 words when possible
- Remove unnecessary adjectives
- Get to the point

✅ Good: "刷新成功" (3 words)
❌ Bad: "您的数据刷新操作已经成功完成了" (冗长)

### 3. Friendly (友好)
- Be polite
- Offer solutions
- Don't blame users

✅ Good: "未找到匹配数据，请调整筛选条件"
❌ Bad: "没数据"

---

## Copy Templates

### Success Messages

| Scenario | Template | Duration |
|----------|----------|----------|
| Data refresh | "数据刷新成功，最新日期: {date}" | 3s |
| Filter applied | "筛选已应用，找到 {count} 条记录" | 2s |
| File exported | "{filename} 已下载" | 2.5s |
| Settings saved | "设置已保存" | 2s |

**Implementation**:
```javascript
export const SUCCESS_MESSAGES = {
  dataRefresh: (date) => `数据刷新成功，最新日期: ${date}`,
  filterApply: (count) => `筛选已应用，找到 ${count.toLocaleString()} 条记录`,
  export: (filename) => `${filename} 已下载`,
  settingsSaved: () => '设置已保存'
}
```

### Error Messages

**Formula**: Problem + Solution

| Scenario | Problem | Solution |
|----------|---------|----------|
| Network error | "无法连接服务器" | "请检查网络连接，或稍后重试" |
| Data load failed | "数据加载失败" | "请刷新页面或联系管理员" |
| Invalid format | "不支持的文件格式" | "请上传 .xlsx 或 .csv 文件" |
| No permission | "无权限访问" | "请联系管理员开通权限" |

**Implementation**:
```javascript
export const ERROR_MESSAGES = {
  network: {
    title: '无法连接服务器',
    message: '请检查网络连接，或稍后重试'
  },
  dataLoad: (reason) => ({
    title: '数据加载失败',
    message: reason || '请刷新页面或联系管理员'
  }),
  invalidFile: {
    title: '不支持的文件格式',
    message: '请上传 .xlsx 或 .csv 文件'
  }
}
```

### Warning Messages

| Severity | Icon | Template |
|----------|------|----------|
| High | ⚠️ | "数据质量评分较低 ({score}分)，建议检查数据源" |
| Medium | 💡 | "发现 {count} 条异常数据，建议人工复核" |
| Low | ℹ️ | "{count} 名业务员未匹配，映射覆盖率 {rate}%" |

### Button Labels

| Action Type | Primary | Secondary | Danger |
|------------|---------|-----------|--------|
| Confirm | "确定" | "取消" | - |
| Submit | "提交" | "重置" | - |
| Save | "保存" | "放弃修改" | - |
| Delete | - | "取消" | "确认删除" |
| Refresh | "立即刷新" | "稍后" | - |

---

## Number Formatting

### Formatter Functions

```javascript
export const formatters = {
  // Premium formatting
  premium(value) {
    if (value == null) return '-'
    const abs = Math.abs(value)
    const sign = value < 0 ? '-' : ''

    if (abs >= 10000) {
      return `${sign}${(abs / 10000).toFixed(1)} 万元`
    }
    return `${sign}${abs.toLocaleString('zh-CN')} 元`
  },

  // Percentage
  percentage(value, decimals = 1) {
    if (value == null) return '-'
    return `${(value * 100).toFixed(decimals)}%`
  },

  // Count
  count(value) {
    if (value == null) return '-'
    return `${value.toLocaleString('zh-CN')} 条`
  },

  // Date
  date(value, format = 'short') {
    if (!value) return '-'
    const d = new Date(value)
    if (format === 'short') {
      return d.toISOString().split('T')[0]  // YYYY-MM-DD
    }
    return `${d.getFullYear()}年${String(d.getMonth()+1).padStart(2,'0')}月${String(d.getDate()).padStart(2,'0')}日`
  }
}
```

### Usage Examples

```javascript
formatters.premium(125420)          // "12.5 万元"
formatters.premium(-5000)           // "-5,000 元"
formatters.percentage(0.235)        // "23.5%"
formatters.count(5123)              // "5,123 条"
formatters.date('2025-11-09')       // "2025-11-09"
formatters.date('2025-11-09', 'long') // "2025年11月09日"
```

---

## Writing Checklist

Before publishing copy:
- [ ] Follows 3 tone principles (professional, concise, friendly)
- [ ] Uses established templates
- [ ] Numbers are formatted correctly
- [ ] Error messages include solutions
- [ ] No emojis (unless user requests)
- [ ] Length: 3-20 words for most messages
- [ ] Tested with longest possible values

---

## Common Mistakes

### ❌ Mistake 1: Too casual
"哇塞！数据好多啊~"

✅ Fix: "数据加载完成，共 5,123 条记录"

### ❌ Mistake 2: Too technical
"NullPointerException in data loader"

✅ Fix: "数据加载失败，请稍后重试"

### ❌ Mistake 3: No solution
"错误"

✅ Fix: "数据加载失败，请刷新页面或联系管理员"

### ❌ Mistake 4: Unformatted numbers
"保费: 1254200"

✅ Fix: "保费: 125.4 万元"

---

## Related Files

**Toast component**: [frontend/src/components/common/Toast.vue](../../../frontend/src/components/common/Toast.vue)
**Format utils**: Create `frontend/src/utils/format.js`
**Copy templates**: Create `frontend/src/utils/copy.js`

**Related Skills**:
- `status-message-components` - Implement status UI components
- `user-guidance-flows` - Write onboarding and help text

---

**Skill Version**: v1.0
**Created**: 2025-11-09
**Focuses On**: Writing copy only (not design or layout)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alongor666) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
