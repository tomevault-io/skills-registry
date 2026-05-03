---
name: fetch-content
description: description: 抓取待处理的 URL 网页内容，转为 Markdown 存储到本地目录。默认处理当天的 URL 文件，也可指定日期。 Use when this capability is needed.
metadata:
  author: dcuplover
---
---
name: fetch-content
description: 抓取待处理的 URL 网页内容，转为 Markdown 存储到本地目录。默认处理当天的 URL 文件，也可指定日期。
---

# fetch-content

抓取 URL 网页内容并转为 Markdown 存储。

## 运行抓取脚本（默认处理当天）：
```bash
python scripts/fetch_page.py
```

## 运行抓取脚本（指定日期）：
```bash
python scripts/fetch_page.py --date 2026-02-14
```

## 输出
如果抓取成功，就输出抓取成功，否则输出抓取失败，并记录失败原因。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dcuplover) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
