---
name: excel-strategy
description: Excel 操作策略指南 - 所有操作使用 Python Use when this capability is needed.
metadata:
  author: hlbbbbbbb
---

# Excel 操作策略指南

## ⚠️ 核心规则（必须遵守！）

```
┌─────────────────────────────────────────────────────┐
│  Python + openpyxl = 所有 Excel 操作                 │
└─────────────────────────────────────────────────────┘
```

---

## 🎯 工具分工

### Python 代码（所有操作）

| 操作类型 | 使用库 |
|---------|-------|
| 读取数据 | openpyxl / pandas |
| 美化/格式化 | openpyxl |
| 数据处理 | pandas + openpyxl |
| 图表生成 | openpyxl.chart |
| 批量操作 | pandas |

---

## 📋 任务判断表

| 用户请求 | 使用方式 |
|---------|---------|
| "查看数据"、"看一下" | Python 读取 |
| "搜索"、"找" | pandas 筛选 |
| "美化"、"排版" | Python openpyxl |
| "修改"、"改一下" | Python openpyxl |
| "加粗"、"颜色"、"边框" | Python openpyxl |
| "格式化"、"货币" | Python openpyxl |
| "图表"、"统计图" | Python openpyxl.chart |
| "公式"、"计算" | Python openpyxl |
| "合并"、"拆分" | Python openpyxl |

---

## 🔄 标准工作流程

```
用户请求 Excel 操作
    ↓
1. 获取文件路径（用户提供或从对话中获取）
    ↓
2. 判断是只读还是修改？
    ↓
只读 → 直接读取并展示
修改 → 先复制再改（原文件不动）
    ↓
3. Python + openpyxl 处理
    ↓
4. 汇报：做了什么 + 文件在哪里
```

---

## ✅ 正确示例

### 查看数据
```
用户: "帮我看看这个表格有什么"

Python 读取文件，输出数据摘要
```

### 美化表格
```
用户: "帮我美化这个表格"

1. 复制原文件 → 新文件
2. Python + openpyxl 执行美化
3. 告诉用户："✅ 美化完成！保存在 xxx_美化版.xlsx"
```

---

## 🐍 Python 美化模板

```bash
python3 << 'EOF'
import shutil
from openpyxl import load_workbook
from openpyxl.styles import Font, PatternFill, Border, Side, Alignment

# 复制原文件（第一次修改时）
original_path = '/path/to/原文件.xlsx'
new_path = '/path/to/原文件_美化版.xlsx'
shutil.copy(original_path, new_path)

# 在副本上修改
wb = load_workbook(new_path)
ws = wb.active

# 边框样式
thin_border = Border(
    left=Side(style='thin'),
    right=Side(style='thin'),
    top=Side(style='thin'),
    bottom=Side(style='thin')
)

# 标题样式
header_fill = PatternFill(start_color='4472C4', end_color='4472C4', fill_type='solid')
header_font = Font(name='SimSun', size=12, bold=True, color='FFFFFF')

# 应用样式
for cell in ws[1]:
    cell.font = header_font
    cell.fill = header_fill
    cell.border = thin_border

wb.save(new_path)
print(f'✅ 美化完成！')
print(f'新文件：{new_path}')
print(f'原文件保持不变：{original_path}')
EOF
```

---

## 📝 总结

1. **所有操作用 Python** - openpyxl / pandas
2. **修改前先复制** - 原文件保持不变
3. **汇报清楚** - 做了什么 + 文件在哪里

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hlbbbbbbb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
