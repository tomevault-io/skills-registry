---
name: skill-creator
description: > Use when this capability is needed.
metadata:
  author: asccclass
---

# Skill Creator — 標準化技能建立範本

此技能本身不執行任何指令，而是作為建立新 Skill 的**參考範本**與**工具包**。

## Purpose

當需要建立新的 PCAI Skill 時，使用此範本確保：
1. 目錄結構符合 agentskills.io 開源規格
2. SKILL.md 格式正確（YAML Frontmatter + Markdown Body）
3. 所有必要欄位已填寫

## 目錄結構

一個符合規格的 Skill 目錄應包含：

```
my_skill/
├── SKILL.md              # [必要] 核心定義檔案（YAML Frontmatter + 說明文件）
├── scripts/              # [選填] 輔助執行腳本
├── templates/            # [選填] 輸出格式範本
└── references/           # [選填] 技術參考文件
```

## Steps

### 建立新 Skill

1. 複製 `templates/SKILL_TEMPLATE.md` 到新目錄
2. 填入 `name`、`description`、`command` 等欄位
3. 執行 `scripts/scaffold` 腳本自動建立骨架（或手動建立）
4. 執行 `scripts/validate` 腳本驗證是否符合規格

### 骨架產生器 (scaffold)

```bash
# Linux/macOS
bash skills/skill-creator/scripts/scaffold.sh my_skill "我的技能描述"

# Windows
powershell -File skills/skill-creator/scripts/scaffold.ps1 my_skill "我的技能描述"
```

### 規格驗證器 (validate)

```bash
# Linux/macOS
bash skills/skill-creator/scripts/validate.sh skills/my_skill

# Windows
powershell -File skills/skill-creator/scripts/validate.ps1 skills/my_skill
```

## Output Format

骨架產生器會建立以下結構：
```
skills/<skill_name>/
├── SKILL.md              # 已填入基本資訊的定義檔
├── scripts/              # 空目錄（供放置腳本）
├── templates/            # 空目錄（供放置範本）
└── references/           # 空目錄（供放置參考文件）
```

## Examples

### 最小可用 Skill（僅需 SKILL.md）

```yaml
---
name: hello_world
description: 回傳 Hello World 訊息
command: echo "Hello, World!"
---
# Hello World Skill
一個簡單的示範技能。
```

### 完整 Skill（含參數、選項、別名）

```yaml
---
name: get_taiwan_weather
description: 查詢台灣各縣市天氣預報
command: web_fetch "https://api.example.com?location={{url:location}}"
cache_duration: 3h
options:
  location:
    - 臺北市
    - 高雄市
option_aliases:
  location:
    "台北": "臺北市"
metadata:
  pcai:
    requires:
      bins: ["curl"]
---
# 天氣查詢技能
透過 API 取得天氣資料。
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/asccclass) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
