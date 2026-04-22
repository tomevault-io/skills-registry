---
name: git
description: Best practices สำหรับการใช้ Git ในการพัฒนาซอฟต์แวร์ Use when this capability is needed.
metadata:
  author: newkub
---

## When to Use

- เริ่มต้นโปรเจกต์ใหม่ที่ต้องการ version control
- ทำงานร่วมกับทีมหลายคน
- ต้องการติดตามการเปลี่ยนแปลงของ source code
- จัดการ deployment และ release

## Quick Start

1. ติดตั้ง Git: `git --version` ตรวจสอบว่าติดตั้งแล้ว
2. ตั้งค่าผู้ใช้: `git config --global user.name "ชื่อของคุณ"` และ `git config --global user.email "email@example.com"`
3. สร้าง repository: `git init` หรือ `git clone <url>`
4. เพิ่มไฟล์: `git add .` และ commit: `git commit -m "Initial commit"`
5. ส่งไปยัง remote: `git push origin main`

## Rules

- [1-git-setup.md](rules/1-git-setup.md) - การติดตั้งและตั้งค่า Git
- [2-git-branching.md](rules/2-git-branching.md) - การจัดการ branch
- [3-git-commit.md](rules/3-git-commit.md) - การทำ commit ที่ดี
- [4-git-collaboration.md](rules/4-git-collaboration.md) - การทำงานร่วมกับทีม
- [5-git-workflow.md](rules/5-git-workflow.md) - การเลือก Git workflow ที่เหมาะสม
- [6-git-history.md](rules/6-git-history.md) - การจัดการประวัติ commits
- [7-git-stash.md](rules/7-git-stash.md) - การใช้ stash สำหรับเก็บการเปลี่ยนแปลงชั่วคราว
- [8-git-ignore.md](rules/8-git-ignore.md) - การตั้งค่า .gitignore อย่างมีประสิทธิภาพ
- [9-git-hooks.md](rules/9-git-hooks.md) - การใช้ Git hooks สำหรับ automation
- [10-git-recovery.md](rules/10-git-recovery.md) - การกู้คืนข้อมูลและแก้ไขปัญหา

## Knowledge

- [core-concept.md](knowledge/core-concept.md) - แนวคิดพื้นฐานของ Git
- [all-features.md](knowledge/all-features.md) - คุณสมบัติทั้งหมดของ Git
- [best-practices/](knowledge/best-practices/) - best practices สำหรับการใช้ Git
  - [commit-messages.md](knowledge/best-practices/commit-messages.md) - การเขียน commit messages ที่ดี
  - [branching-strategy.md](knowledge/best-practices/branching-strategy.md) - กลยุทธ์การจัดการ branches
  - [collaboration.md](knowledge/best-practices/collaboration.md) - การทำงานร่วมกับทีม
  - [performance.md](knowledge/best-practices/performance.md) - การปรับปรุงประสิทธิภาพ
  - [security.md](knowledge/best-practices/security.md) - การรักษาความปลอดภัย
  - [troubleshooting.md](knowledge/best-practices/troubleshooting.md) - การแก้ไขปัญหา
  - [automation.md](knowledge/best-practices/automation.md) - การทำงานอัตโนมัติ

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newkub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
