---
name: search
description: Code Search: เครื่องมือและเทคนิคสำหรับการค้นหาโค้ดอย่างมีประสิทธิภาพด้วย ast-grep, ripgrep (rg), และ fd Use when this capability is needed.
metadata:
  author: newkub
---

# Code Search Tools

## When to Apply

ใช้ Skill นี้เมื่อต้องการค้นหาโค้ด, ไฟล์, หรือโครงสร้างโค้ดภายในโปรเจกต์อย่างรวดเร็วและมีประสิทธิภาพ

- เมื่อต้องการค้นหาตามโครงสร้างโค้ด (AST) แทนการค้นหาแบบข้อความธรรมดา
- เมื่อต้องการค้นหาข้อความในไฟล์จำนวนมากอย่างรวดเร็ว
- เมื่อต้องการค้นหาไฟล์หรือไดเรกทอรีด้วยวิธีที่ง่ายและเร็วกว่า `find`

## Rules

| Priority | Impact | Reference | Name | Description | Prefix | Condition |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `CRITICAL` | [cs-ast-grep.md](./rules/cs-ast-grep.md) | AST Grep | ค้นหาโค้ดตามโครงสร้าง (AST) | `cs-` | เมื่อค้นหาโครงสร้างโค้ด |
| 2 | `HIGH` | [cs-ripgrep.md](./rules/cs-ripgrep.md) | Ripgrep | ค้นหาข้อความในไฟล์อย่างรวดเร็ว | `cs-` | เมื่อค้นหาข้อความ |
| 3 | `HIGH` | [cs-fd.md](./rules/cs-fd.md) | FD | ค้นหาไฟล์และไดเรกทอรี | `cs-` | เมื่อค้นหาไฟล์ |

## Knowledge

| Reference | Name | Description | Prefix |
| :--- | :--- | :--- | :--- |

## Overview

### Rules

แต่ละไฟล์ Rule ประกอบด้วย:

- เหตุผล (Why)
- ตัวอย่างที่ไม่ดี (Anti-patterns)
- ตัวอย่างที่ดี (Best practices)
- กฎที่ต้องปฏิบัติตาม (Rules)
- ผลกระทบถ้าไม่ทำตาม (Impact)
- เอกสารอ้างอิง (References)

### Knowledge

แต่ละไฟล์ Knowledge ประกอบด้วย:

- Overview: ภาพรวมของ topic
- Key Concepts: concepts สำคัญที่ต้องรู้
- Examples: ตัวอย่างการใช้งาน
- Best Practices: best practices ที่ควรทำตาม
- References: ลิงก์ไปยังแหล่งข้อมูลต้นฉบับ

## How to Use

แต่ละไฟล์ Rule อธิบายถึง:

- เหตุผลที่ต้องทำตามกฎ
- ตัวอย่างที่ไม่ดีและดี
- กฎที่ต้องปฏิบัติตาม
- ผลกระทบถ้าไม่ทำตาม
- เอกสารอ้างอิง

แต่ละไฟล์ Knowledge อธิบายถึง:

- ภาพรวมของ topic
- Concepts สำคัญที่ต้องรู้
- ตัวอย่างการใช้งาน
- Best practices ที่ควรทำตาม
- เอกสารอ้างอิง

## References

- [AST Grep Documentation](https://ast-grep.github.io/)
- [Ripgrep Documentation](https://github.com/BurntSushi/ripgrep)
- [FD Documentation](https://github.com/sharkdp/fd)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newkub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
