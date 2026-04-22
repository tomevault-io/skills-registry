---
name: shell-script-refactor
description: Acts as a Bash scripting expert that creates and refactors Shell scripts with clean structure, standard conventions, strong error handling, logging, and production-ready best practices. Use when this capability is needed.
metadata:
  author: natthasath
---

สร้างไฟล์ Shell Script สำหรับ {xxx} และช่วยปรับปรุงโครงสร้างและ code ของ Shell Script ให้เป็นไปตามมาตรฐานการพัฒนาที่ดี และเรียงลำดับ properties ตามหลักการต่อไปนี้:

1. โครงสร้าง Shell Script
ปรับแต่ง script ให้เรียงลำดับและจัดกลุ่มตามมาตรฐาน:
```
- #!/bin/bash
- คำอธิบาย script และการใช้งาน
- ประกาศตัวแปรค่าคงที่ (Constants)
  - TIMEZONE="Asia/Bangkok"
  - ตัวแปรอื่นๆ
- ประกาศฟังก์ชัน (Functions)
- การตรวจสอบเงื่อนไขก่อนทำงาน
- โค้ดหลัก (Main execution)
- สรุปผลการทำงาน
```

2. Naming Convention
- ตัวแปรค่าคงที่: ใช้ตัวพิมพ์ใหญ่ทั้งหมด เช่น INPUT_FILE, CONFIG_DIR, TIMEZONE
- ตัวแปรทั่วไป: ใช้ตัวพิมพ์เล็ก และ underscore เช่น current_item, total_count
- ฟังก์ชัน: ใช้ตัวพิมพ์เล็ก และ underscore เช่น check_dependencies(), process_item()
- ชื่อฟังก์ชันควรสื่อถึงการกระทำ (action verb) เช่น validate_input(), execute_command()

3. การจัดการข้อผิดพลาด
- กำหนดฟังก์ชัน error_exit() สำหรับแสดงข้อผิดพลาดและออกจากโปรแกรม
- ตรวจสอบพารามิเตอร์และเงื่อนไขก่อนทำงาน
- กำหนดค่า return code ที่ชัดเจน (0=success, 1=general error, 2=missing dependencies)

4. ฟังก์ชัน แยกโค้ดเป็นฟังก์ชันตามความรับผิดชอบดังนี้:
- log_message() - บันทึกข้อความลงไฟล์ log
- check_dependencies() - ตรวจสอบโปรแกรมที่จำเป็น
- validate_input() - ตรวจสอบความถูกต้องของ input
- process_item() - ประมวลผลรายการแต่ละรายการ
- execute_command() - รันคำสั่งหลัก
- display_summary() - แสดงผลสรุป

5. การบันทึก Log
- ใช้ฟังก์ชันเดียวสำหรับการบันทึก log ทั้งหมด
- กำหนดรูปแบบ timestamp ที่สม่ำเสมอ
- แยกระดับความสำคัญ (INFO, WARNING, ERROR)

6. ข้อกำหนดพิเศษ
- เพิ่มความสามารถในการรับพารามิเตอร์จาก command line
- ใช้ signal trap เพื่อจัดการกรณีถูกยกเลิก
- เพิ่ม progress bar หรือตัวแสดงสถานะการทำงาน
- รองรับการทำงานในโหมด verbose และ silent

กรุณาปรับปรุง script ที่มีอยู่หรือสร้างตัวอย่างโครงสร้างใหม่ที่เป็นไปตามมาตรฐานข้างต้น ให้ครบทุกข้อ ไม่ต้องอธิบายเหตุผล หรือสรุปอะไรก็ตาม แต่ถ้ามีข้อเสนอแนะในการปรับปรุง script ที่ต่างออกไป ให้เสนอมาพร้อมอธิบายเหตุผลมาด้วย

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/natthasath) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
