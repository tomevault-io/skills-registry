---
name: boq
description: Comprehensive BOQ (Bill of Quantities) creation and management for construction projects. This skill should be used when Claude needs to: (1) Create new BOQ files with standard work categories, (2) Add items to existing BOQ, (3) Manage construction cost estimates with material and labor breakdowns, (4) Generate Thai construction project cost sheets with proper formatting Use when this capability is needed.
metadata:
  author: pobim
---

# BOQ (Bill of Quantities) Skill

ทักษะสำหรับจัดทำและจัดการใบประมาณราคาค่าก่อสร้าง (BOQ) ด้วย Excel

**📁 Output Location:** ไฟล์ BOQ ที่สร้างจะถูกบันทึกไปที่ `workspace/boq_examples/` เพื่อแยกออกจาก skill folder

**📚 Best Practices:** อ่านแนวทางการจัดทำ BOQ ที่ถูกต้องได้ที่ `BEST_PRACTICES.md`

## หมวดงานมาตรฐาน

BOQ ทุกไฟล์จะมี 5 หมวดงานหลัก:
1. **งานเตรียมการ** - งานรื้อถอน, ปรับพื้นที่, ขนขยะ
2. **งานโครงสร้าง** - งานเทคอนกรีต, เหล็กเสริม, โครงสร้างหลัก
3. **งานสถาปัตยกรรม** - งานผนัง, งานฝ้า, งานสี, งานพื้น
4. **งานระบบไฟฟ้า** - ระบบไฟฟ้า, ระบบแสงสว่าง, เต้ารับ
5. **งานระบบสุขาภิบาล** - งานประปา, งานท่อ, สุขภัณฑ์

## โครงสร้างตาราง BOQ

แต่ละ BOQ จะมีคอลัมน์:
- **ลำดับ** - เลขที่รายการ
- **รายการ** - รายละเอียดงาน
- **ปริมาณ** - จำนวน
- **หน่วย** - หน่วยนับ (ตร.ม., ตร.วา, จุด, ชุด, etc.)
- **ค่าวัสดุต่อหน่วย** - ราคาวัสดุต่อหน่วย
- **ราคารวมวัสดุ** - คำนวณอัตโนมัติ: ปริมาณ × ค่าวัสดุต่อหน่วย
- **ค่าแรงต่อหน่วย** - ค่าแรงต่อหน่วย
- **ค่าแรงรวม** - คำนวณอัตโนมัติ: ปริมาณ × ค่าแรงต่อหน่วย
- **รวมราคา** - คำนวณอัตโนมัติ: ราคารวมวัสดุ + ค่าแรงรวม
- **หมายเหตุ** - หมายเหตุเพิ่มเติม

## การใช้งาน

### 1. สร้าง BOQ ใหม่

Use the `boq_helper.py` script to create a new BOQ:

```bash
python boq_helper.py create <filename> [project_name] [location] [customer]
```

**Examples:**

```bash
# สร้าง BOQ พื้นฐาน
python boq_helper.py create project_boq.xlsx

# สร้าง BOQ พร้อมข้อมูลโครงการ
python boq_helper.py create office_renovation.xlsx "โครงการปรับปรุงสำนักงาน" "กรุงเทพมหานคร" "บริษัท ABC จำกัด"
```

The script creates a formatted Excel file with:
- Project information header (ข้อมูลโครงการ, สถานที่, ลูกค้า)
- All 5 standard work categories with space for 3 items each
- Automatic calculation formulas for costs
- Summary rows for each category
- Grand total at the bottom
- Professional formatting with colors and borders

### 2. เพิ่มรายการใน BOQ

Add items to specific categories:

```bash
python boq_helper.py add <filename> <category> <item_no> <description> <quantity> <unit> [material_cost] [labor_cost] [note]
```

**Parameters:**
- `filename`: BOQ file path
- `category`: One of the 5 standard categories (exact match required)
- `item_no`: Item number (e.g., "1.1", "2.1")
- `description`: Item description
- `quantity`: Quantity (number)
- `unit`: Unit (e.g., "ตร.ม.", "ตร.วา", "จุด", "ชุด")
- `material_cost`: Material cost per unit (optional, default: 0)
- `labor_cost`: Labor cost per unit (optional, default: 0)
- `note`: Additional notes (optional)

**Example:**

```bash
python boq_helper.py add office_renovation.xlsx "งานเตรียมการ" "1.1" "รื้อถอนผนังเก่า" 25.5 "ตร.ม." 50 150 "รวมขนย้ายเศษวัสดุ"
```

### 3. คำนวณ Formula ใหม่

After adding items, recalculate all formulas:

```bash
python recalc.py <filename>
```

**Example:**
```bash
python recalc.py office_renovation.xlsx
```

The recalc script:
- Recalculates all formulas (ราคารวมวัสดุ, ค่าแรงรวม, รวมราคา, etc.)
- Updates summary rows for each category
- Updates grand total
- Checks for Excel errors (#REF!, #DIV/0!, etc.)
- Returns JSON with status and any errors found

## Workflow Example

Complete workflow for creating a BOQ:

```python
import subprocess
import json

# 1. Create new BOQ
result = subprocess.run([
    'python', 'boq_helper.py', 'create',
    'renovation.xlsx',
    'โครงการปรับปรุงอาคาร',
    'กรุงเทพฯ',
    'บริษัท XYZ'
], capture_output=True, text=True)
print(json.loads(result.stdout))

# 2. Add items to different categories
items = [
    ('งานเตรียมการ', '1.1', 'รื้อถอนผนัง', 30, 'ตร.ม.', 50, 150),
    ('งานโครงสร้าง', '2.1', 'เทพื้นคอนกรีต', 50, 'ตร.ม.', 800, 400),
    ('งานสถาปัตยกรรม', '3.1', 'ทาสีภายใน', 100, 'ตร.ม.', 80, 120),
]

for category, no, desc, qty, unit, mat, lab in items:
    subprocess.run([
        'python', 'boq_helper.py', 'add',
        'renovation.xlsx', category, no, desc,
        str(qty), unit, str(mat), str(lab)
    ], capture_output=True, text=True)

# 3. Recalculate formulas
result = subprocess.run([
    'python', 'recalc.py', 'renovation.xlsx'
], capture_output=True, text=True)
print(json.loads(result.stdout))
```

## Using Python Directly (Alternative Method)

For more flexibility, directly use openpyxl with the xlsx skill patterns:

```python
from openpyxl import load_workbook

wb = load_workbook('renovation.xlsx')
ws = wb.active

# Add custom item to specific row
row = 8  # Example row number
ws[f'A{row}'] = '1.2'
ws[f'B{row}'] = 'งานขุดดิน'
ws[f'C{row}'] = 15.5
ws[f'D{row}'] = 'ลบ.ม.'
ws[f'E{row}'] = 200
ws[f'F{row}'] = f'=C{row}*E{row}'  # Auto-calculate
ws[f'G{row}'] = 300
ws[f'H{row}'] = f'=C{row}*G{row}'  # Auto-calculate
ws[f'I{row}'] = f'=F{row}+H{row}'  # Total
ws[f'J{row}'] = 'รวมค่าขนย้ายดิน'

wb.save('renovation.xlsx')
```

Then recalculate:
```bash
python recalc.py renovation.xlsx
```

## Important Notes

### Formula Usage
- **ALWAYS use Excel formulas** instead of calculating in Python
- Formulas ensure the BOQ remains dynamic and updateable
- Let Excel calculate: ราคารวมวัสดุ, ค่าแรงรวม, รวมราคา

### Category Limits
- Each category can hold up to 3 items by default
- To add more items, modify the BOQ structure or use Python directly

### Recalculation
- **MANDATORY**: Always run `recalc.py` after creating or modifying BOQ
- This ensures all formulas are calculated correctly
- Check the JSON output for any errors

### Thai Language Support
- All output uses Thai language
- File names can be in Thai or English
- Category names must match exactly (including Thai characters)

### Error Handling
- Scripts return JSON output for easy parsing
- Check `status` field: "success" or "error"
- Error messages are in Thai for clarity

## Bundled Scripts

- `boq_helper.py` - Main BOQ creation and management script
- `recalc.py` - Formula recalculation script
- `validate_boq.py` - Validation script for checking budget proportions
- `BEST_PRACTICES.md` - Guidelines for creating proper BOQ

## Best Practices

สัดส่วนงบประมาณมาตรฐาน (สำหรับบ้านพักอาศัย):
- **งานเตรียมการ:** ≤ 5%
- **งานโครงสร้าง:** 28-32% (เป้าหมาย 30%)
- **งานสถาปัตยกรรม:** 38-42% (เป้าหมาย 40%)
- **งานระบบไฟฟ้า:** 10-14% (เป้าหมาย 12%)
- **งานระบบสุขาภิบาล:** 12-16% (เป้าหมาย 13%)

อ่านรายละเอียดเพิ่มเติมใน `BEST_PRACTICES.md`

## Examples

ตัวอย่างการใช้งานอยู่ที่ `examples/` folder:

```bash
# สร้าง BOQ บ้านพักอาศัย 2 ชั้น (ตาม Best Practices)
python examples/create_house_boq.py
```

ไฟล์ที่สร้างจะอยู่ที่: `workspace/boq_examples/`

## Validation

ตรวจสอบสัดส่วนงบประมาณ:

```python
from validate_boq import validate_from_data

boq_totals = {
    "งานเตรียมการ": 186916,
    "งานโครงสร้าง": 1122092,
    "งานสถาปัตยกรรม": 1495327,
    "งานระบบไฟฟ้า": 448598,
    "งานระบบสุขาภิบาล": 485981
}

validate_from_data(boq_totals)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pobim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
