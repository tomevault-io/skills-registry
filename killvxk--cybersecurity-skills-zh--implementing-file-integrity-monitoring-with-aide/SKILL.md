---
name: implementing-file-integrity-monitoring-with-aide
description: 配置 AIDE（高级入侵检测环境）进行文件完整性监控，包括基线创建、定期完整性检查、变更检测和告警 Use when this capability is needed.
metadata:
  author: killvxk
---

# 使用 AIDE 实施文件完整性监控

## 概述

AIDE（高级入侵检测环境）是一个基于主机的入侵检测系统，使用加密校验和监控文件和目录完整性。本技能涵盖生成 AIDE 配置文件、初始化基线数据库、运行完整性检查、解析变更报告，以及设置基于 cron 的自动化监控和告警。

## 前置条件

- 目标 Linux 系统上已安装 AIDE（apt install aide / yum install aide）
- 用于文件系统扫描的 root 或 sudo 访问权限
- Python 3.8+ 及标准库

## 步骤

1. **生成 AIDE 配置** — 创建包含关键目录（/etc、/bin、/sbin、/usr/bin、/boot）监控规则的 aide.conf
2. **初始化基线数据库** — 运行 aide --init 创建初始文件完整性基线
3. **运行完整性检查** — 执行 aide --check 将当前状态与基线对比
4. **解析变更报告** — 从 AIDE 输出中提取新增、删除和变更的文件
5. **配置自动化监控** — 生成 cron 定时完整性检查任务
6. **生成合规报告** — 生成包含严重性分类的所有文件变更结构化报告

## 预期输出

- AIDE 配置文件（aide.conf）
- 基线数据库创建状态
- 文件变更（新增/删除/变更）的 JSON 报告（含严重性）
- 自动化监控的 cron 任务配置

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
