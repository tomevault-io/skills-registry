---
name: contractor-marketing-cowork
description: **Cowork插件：专为承包商和家庭服务企业提供的人工智能营销解决方案。** Use when this capability is needed.
metadata:
  author: AgentWorkers
---
# 承包商营销 — Cowork 插件

专为承包商和家庭服务企业设计的 AI 营销工具。由承包商自行开发，而非营销机构。

## 命令

| 命令 | 功能 |
|---------|-------------|
| `/contractor-marketing:onboard` | 设置您的企业资料（仅运行一次） |
| `/contractor-marketing:gbp-post` | 生成并发布 Google Business Profile 的相关内容 |
| `/contractor-marketing:review-response` | 回复客户评价（支持批量处理） |
| `/contractor-marketing:social-batch` | 生成一周的社交媒体发布内容 |
| `/contractor-marketing:weekly-report` | 提供 SEO 和广告效果报告 |
| `/contractor-marketing:ad-creative` | 生成 Facebook、Instagram 和 Google 的广告创意 |
| `/contractor-marketing:content-calendar` | 制定整个月的内容计划及 4 篇博客文章的草稿 |
| `/contractor-marketing:competitor-audit` | 每月进行竞争对手分析 |
| `/contractor-marketing:proposal` | 生成专业的报价单（例如：“Mike，2 英亩土地，4,600 美元”） |
| `/contractor-marketing:job-cost` | 跟踪项目的盈利情况和利润率 |
| `/contractor-marketing:email-sequence` | 自动发送邮件序列 |
| `/contractor-marketing:lead-followup` | 提供带有发送时间的潜在客户跟进模板 |

## 相关技能

Claude 会自动应用以下相关技能：

- **contractor-seo**：本地 SEO、引用、关键词优化、服务区域页面优化 |
- **contractor-ads**：Meta/Google 广告活动、预算制定、广告创意设计 |
- **contractor-social**：内容策略、社交媒体平台规则、客户评价处理 |
- **contractor-email**：自动化邮件发送、潜在客户跟进、新闻通讯管理 |
- **contractor-operations**：报价单制作、项目成本核算、定价策略 |
- **contractor-positioning**：企业定位、信息传递、差异化竞争策略 |

## 策略库

通过 API 与 Heavy Metric 策略库（包含 74 种经过验证的营销策略）连接：

```bash
curl -s "https://dmlybcnpwtnaadmapdhl.supabase.co/rest/v1/strategies?or=(title.ilike.*QUERY*,category.ilike.*QUERY*)&select=title,slug,content&apikey=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImRtbHliY25wd3RuYWFkbWFwZGhsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NDMxOTk4NzMsImV4cCI6MjA1ODc3NTg3M30.kVMGdVCPJMFwiVn-OWpMFIGJWJCYzaOGxFsZPJSq5s4" \
  -H "Content-Type: application/json"
```

## 集成工具

支持直接集成或通过浏览器自动化工具进行操作：
Google Business Profile、Meta Ads、Google Ads、Buffer/Later、Mailchimp/MailerLite、Google Search Console、GA4、Yelp/BBB、Jobber/HouseCall Pro

## 文体风格

以承包商的视角撰写内容，而非营销机构的官方语言。

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
