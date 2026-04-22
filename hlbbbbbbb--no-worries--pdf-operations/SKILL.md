---
name: pdf-operations
description: PDF 文件操作技能。使用内置 Python 库读取、提取、生成 PDF。支持表格提取、文本读取、PDF 合并/拆分等。 Use when this capability is needed.
metadata:
  author: hlbbbbbbb
---

# PDF 操作技能

## 可用库（已预装）

| 库 | 用途 |
|---|---|
| `pypdf` | PDF 读取、合并、拆分、元数据操作 |
| `pdfplumber` | PDF 表格提取（推荐用于表格） |
| `reportlab` | 生成/创建新 PDF |
| `pillow` | PDF 转图片（配合使用） |

## 常用操作示例

### 1. 读取 PDF 文本

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")
print(f"总页数: {len(reader.pages)}")

for i, page in enumerate(reader.pages):
    text = page.extract_text()
    print(f"=== 第 {i+1} 页 ===")
    print(text)
```

### 2. 提取 PDF 表格（推荐 pdfplumber）

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for i, page in enumerate(pdf.pages):
        print(f"=== 第 {i+1} 页 ===")
        
        # 提取表格
        tables = page.extract_tables()
        for j, table in enumerate(tables):
            print(f"表格 {j+1}:")
            for row in table:
                print(row)
        
        # 提取文本
        text = page.extract_text()
        print(text)
```

### 3. PDF 转 DataFrame（配合 pandas）

```python
import pdfplumber
import pandas as pd

with pdfplumber.open("document.pdf") as pdf:
    page = pdf.pages[0]
    tables = page.extract_tables()
    
    if tables:
        # 第一行作为表头
        df = pd.DataFrame(tables[0][1:], columns=tables[0][0])
        print(df)
        
        # 保存为 Excel
        df.to_excel("output.xlsx", index=False)
```

### 4. 合并多个 PDF

```python
from pypdf import PdfWriter

merger = PdfWriter()

# 添加多个 PDF
merger.append("file1.pdf")
merger.append("file2.pdf")
merger.append("file3.pdf")

# 保存
merger.write("merged.pdf")
merger.close()
```

### 5. 拆分 PDF

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")

# 提取第 1-3 页
writer = PdfWriter()
for i in range(3):  # 0, 1, 2
    writer.add_page(reader.pages[i])

writer.write("first_3_pages.pdf")
writer.close()
```

### 6. 生成新 PDF（带中文支持）

```python
from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont

# ⚠️ 必须注册中文字体，否则中文显示为方框！
# macOS 系统自带的中文字体
CHINESE_FONTS = [
    ('/System/Library/Fonts/STHeiti Light.ttc', 'STHeiti'),
    ('/System/Library/Fonts/STHeiti Medium.ttc', 'STHeitiMedium'),
    ('/Library/Fonts/Arial Unicode.ttf', 'ArialUnicode'),
    ('/System/Library/Fonts/Hiragino Sans GB.ttc', 'HiraginoSansGB'),
]

# 注册第一个可用的中文字体
chinese_font = None
for font_path, font_name in CHINESE_FONTS:
    try:
        import os
        if os.path.exists(font_path):
            pdfmetrics.registerFont(TTFont(font_name, font_path))
            chinese_font = font_name
            break
    except:
        continue

if not chinese_font:
    print("⚠️ 未找到中文字体，中文可能显示为方框")
    chinese_font = 'Helvetica'

# 创建 PDF
c = canvas.Canvas("new_document.pdf", pagesize=A4)
c.setFont(chinese_font, 12)
c.drawString(100, 750, "Hello, 你好世界！这是中文测试")
c.drawString(100, 720, "第二行内容：数据分析报告")
c.save()
print("✅ PDF 已生成")
```

### 7. PDF 页面信息

```python
from pypdf import PdfReader

reader = PdfReader("document.pdf")

# 文档信息
print(f"页数: {len(reader.pages)}")
print(f"元数据: {reader.metadata}")

# 页面尺寸
page = reader.pages[0]
print(f"宽度: {page.mediabox.width}")
print(f"高度: {page.mediabox.height}")
```

### 8. matplotlib 中文图表（保存为图片或嵌入 PDF）

```python
import matplotlib.pyplot as plt
import matplotlib
import os

# ⚠️ 必须设置中文字体，否则图表中文显示为方框！
# macOS 系统字体设置
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS', 'Heiti TC', 'STHeiti', 'SimHei']
plt.rcParams['axes.unicode_minus'] = False  # 解决负号显示问题

# 示例：创建中文柱状图
categories = ['一月', '二月', '三月', '四月', '五月']
values = [23, 45, 56, 78, 32]

fig, ax = plt.subplots(figsize=(10, 6))
bars = ax.bar(categories, values, color=['#4CAF50', '#2196F3', '#FFC107', '#E91E63', '#9C27B0'])

ax.set_xlabel('月份', fontsize=12)
ax.set_ylabel('销售额（万元）', fontsize=12)
ax.set_title('2024年月度销售数据分析', fontsize=14, fontweight='bold')

# 在柱子上显示数值
for bar, val in zip(bars, values):
    ax.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 1, 
            str(val), ha='center', fontsize=10)

plt.tight_layout()
plt.savefig('sales_chart.png', dpi=150, bbox_inches='tight')
print("✅ 图表已保存为 sales_chart.png")
plt.close()
```

### 9. 将 matplotlib 图表嵌入 PDF

```python
import matplotlib.pyplot as plt
from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas
from reportlab.lib.utils import ImageReader
import io

# 设置 matplotlib 中文
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS', 'Heiti TC', 'STHeiti']
plt.rcParams['axes.unicode_minus'] = False

# 创建图表
fig, ax = plt.subplots(figsize=(8, 5))
ax.pie([30, 25, 20, 15, 10], labels=['产品A', '产品B', '产品C', '产品D', '其他'],
       autopct='%1.1f%%', colors=['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FFEAA7'])
ax.set_title('产品销售占比', fontsize=14)

# 保存到内存
img_buffer = io.BytesIO()
plt.savefig(img_buffer, format='png', dpi=150, bbox_inches='tight')
img_buffer.seek(0)
plt.close()

# 创建 PDF 并嵌入图表
c = canvas.Canvas("report_with_chart.pdf", pagesize=A4)

# 添加标题（需要先注册中文字体，参考示例 6）
c.setFont('Helvetica-Bold', 16)
c.drawString(200, 780, "Sales Report")

# 嵌入图表
img = ImageReader(img_buffer)
c.drawImage(img, 50, 350, width=500, height=300)

c.save()
print("✅ 带图表的 PDF 已生成")
```

## 注意事项

1. **中文支持**: 
   - reportlab 生成 PDF 时**必须**注册中文字体
   - matplotlib 生成图表时**必须**设置 `font.sans-serif`
   - macOS 推荐字体: `Arial Unicode MS`, `Heiti TC`, `STHeiti`
2. **表格提取**: pdfplumber 比 pypdf 更擅长表格提取
3. **扫描件 PDF**: 如果 PDF 是扫描件（图片），需要 OCR，建议告知用户
4. **文件路径**: 使用完整路径避免问题
5. **大文件**: 大 PDF 文件建议分页处理

## 常见任务映射

| 用户需求 | 使用库 | 方法 |
|---------|-------|------|
| "读取 PDF" | pypdf | PdfReader + extract_text() |
| "提取表格" | pdfplumber | page.extract_tables() |
| "PDF 转 Excel" | pdfplumber + pandas | extract_tables() → DataFrame |
| "合并 PDF" | pypdf | PdfWriter.append() |
| "拆分 PDF" | pypdf | PdfWriter.add_page() |
| "生成 PDF" | reportlab | Canvas |
| "PDF 转图片" | 需要外部工具 | 建议用预览应用打开 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hlbbbbbbb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
