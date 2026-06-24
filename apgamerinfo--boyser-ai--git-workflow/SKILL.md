---
name: git-workflow
description: จัดการ branch, rebase, merge, PR, แก้ conflict ใช้เมื่อทำงานกับ branch หรือ git ที่ซับซ้อนกว่าแค่ commit Use when this capability is needed.
metadata:
  author: apgamerinfo
---

# Skill: git-workflow

1. **แตก branch จาก main ล่าสุด**: `git switch main && git pull` แล้ว `git switch -c feat/xxx` — branch เล็ก โฟกัสเรื่องเดียว
2. commit ระหว่างทางบ่อยๆ (ดู skill git-commit)
3. **อัปเดต branch**: `git pull --rebase` หรือ `git rebase main` ให้ประวัติสะอาด (อย่า rebase branch ที่ push ร่วมกับคนอื่นแล้ว)
4. **แก้ conflict**: อ่านทั้งสองฝั่งให้เข้าใจ (`<<<<<<<` ของเรา / `>>>>>>>` ของเขา), เลือก/รวมให้ถูก, ลบ marker, **รันเทสต์หลังแก้**
5. **PR**: เขียนอธิบาย what / why / how to test ให้ผู้รีวิวเข้าใจ
6. กฎเหล็ก: **ห้าม `git push --force` บน branch ร่วม** (ใช้ `--force-with-lease` ถ้าจำเป็นบน branch ตัวเอง); **ห้าม push เว้นแต่ผู้ใช้สั่ง**
7. ถ้าพลาด: `git reflog` ช่วยกู้ได้เกือบทุกอย่าง — อย่าเพิ่งตกใจ

---
> Source: [apgamerinfo/boyser-ai](https://github.com/apgamerinfo/boyser-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
