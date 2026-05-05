---
name: fanpage-article-writer
description: 粉絲專頁文章自動化寫作流程。支援資料搜尋、文章撰寫、爆款標題生成、排版優化、AI配圖生成。當用戶提到寫粉專、FB貼文、社群文章、爆款文章、內容創作時使用此 skill。 Use when this capability is needed.
metadata:
  author: neversight
---

# 粉絲專頁文章寫作流程

6 步完成高品質粉專文章：搜尋資料 → 撰寫文章 → 生成標題 → 排版優化 → 生成配圖 → 引用文獻

---

## 重要規範（必讀）

### 用語規範：使用台灣正體中文

**禁止使用中國用語，必須使用台灣用語：**

| ❌ 中國用語 | ✅ 台灣用語 |
|------------|------------|
| 硅 | 矽 (Si) |
| 視頻 | 影片 |
| 軟件 | 軟體 |
| 硬件 | 硬體 |
| 信息 | 資訊 |
| 網絡 | 網路 |
| 數據 | 資料 |
| 優化 | 最佳化 |
| 激活 | 啟用 |
| 默認 | 預設 |
| 交互 | 互動 |
| 鏈接 | 連結 |
| 博客 | 部落格 |
| 用戶 | 使用者 |
| 人工智能 | 人工智慧 |

---

## Step 1: 搜尋資料

使用 WebSearch 工具搜尋主題相關資料：

```
搜尋要求：
1. 並行搜尋多個來源（官方文件、X/Twitter、Reddit、技術論壇）
2. 優先取得最新資料（當月/當季）
3. 可啟動多個並行 Task 加速搜尋
4. 深度總結搜尋結果
```

### 特殊領域搜尋規範

**營養學、運動科學、健康相關主題：**

1. **優先搜尋 PubMed** (https://pubmed.ncbi.nlm.nih.gov/)
   - 搜尋關鍵字加上 `site:pubmed.ncbi.nlm.nih.gov`
   - 或直接搜尋 `[主題] PubMed research study`
2. 尋找 systematic review 或 meta-analysis 優先
3. 注意研究的發表年份，優先引用近 5 年內的研究
4. 記錄論文標題、作者、期刊、年份、PMID

---

## Step 2: 撰寫文章

**必須先讀取用戶的 CLAUDE.md 獲取寫作風格**

撰寫要求：
- 1000-1500 字
- 故事化開頭，帶情感色彩（興奮/焦慮/好奇）
- 準備 2-3 個備選標題
- 結構：效果展示 → 問題描述 → 步驟教學 → 昇華總結
- 遵循 CLAUDE.md 中定義的寫作風格和結尾語
- **全文使用台灣正體中文用語**

### 營養/健康類文章：菌種與菌株標註規範

涉及微生物（益生菌、發酵食品）的文章，必須明確區分：

| 層級 | 定義 | 範例 |
|------|------|------|
| **菌屬** | 大分類 | Lactobacillus（乳酸桿菌屬） |
| **菌種** | 中分類 | *Lactobacillus plantarum*（植物乳桿菌） |
| **菌株** | 具體編號 | *L. plantarum* **KU200656** |

**重要：每個健康功效聲稱，必須在該段落下方立即附上研究連結**

```markdown
### ⭐ Lactobacillus plantarum KU200656

| 功效 | 數據 |
|------|------|
| 胃酸耐受性 | **99.48%** |
| 膽鹽耐受性 | **102.40%** |

> 📚 **研究支持**：Jang HJ et al. (2021). *Food Sci Biotechnol*, 30(1), 97-106.
> https://pmc.ncbi.nlm.nih.gov/articles/PMC7847474/
```

---

## Step 3: 生成標題

生成 5 個爆款標題，特點：
- **痛點明確**：直擊讀者痛處（「還在手動...」）
- **數字吸引**：具體數字更有說服力（「3分鐘」、「5個技巧」）
- **結果導向**：承諾具體收益（「效率暴漲10倍」）
- **情緒調動**：驚、神技、秘笈等詞彙
- **懸念設置**：引發好奇心

---

## Step 4: 排版優化

優化建議：
1. **段落結構**：每段 3-5 行，重要數據單獨成段加粗
2. **配圖位置**：標題下方放置一張精準的封面圖
3. **程式碼區塊**：前後留白，語法高亮
4. **金句**：單獨成段，增強記憶點

---

## Step 5: 生成配圖（AI 圖片生成）

使用 Gemini API 生成 **一張精準的配圖**。

### 核心原則：一張精準勝過三張平庸

**不要生成多張圖片！** 專注於一張能傳達文章核心 vibe 的高品質配圖。

### Prompt 設計：對應標題 Vibe

生成圖片前，先分析標題的情緒和關鍵詞：

| 標題關鍵詞 | Vibe | Prompt 對應 |
|-----------|------|-------------|
| 數字（100億、3分鐘） | 震撼、豐富 | `extreme close-up`, `macro` |
| 揭密、秘密 | 發現、好奇 | `dramatic lighting`, `revealing` |
| 超強、神技 | 力量、專業 | `powerful`, `professional` |
| 日常食物類 | 親近、食慾 | `mouth-watering`, `warm lighting` |
| 健康、養生 | 活力、自然 | `vibrant`, `natural`, `fresh` |
| 新聞、時事 | 嚴肅、即時 | `news photography`, `dramatic` |

### Prompt 公式

```
[構圖/視角] + [主體描述] + [細節/質感] + [光線/氛圍] + [風格] + [品質]
```

**範例：泡菜益生菌文章**

標題：「一口泡菜 = 100 億益生菌！」

Vibe 分析：「一口」→ 微距特寫 / 「100億」→ 活躍豐富 / 「益生菌」→ 發酵、活的

Prompt：
```
Extreme close-up of glistening Korean kimchi, vibrant red-orange
fermented napa cabbage with visible tiny bubbles from active
fermentation. Dramatic macro food photography with shallow depth
of field, warm golden backlight creating a magical glow.
Mouth-watering, alive, powerful. Editorial magazine cover quality
```

---

### API Key 管理

腳本會自動從 `api_keys.json` 讀取有效的 API Key，並檢查到期日。

**目前已設定的 API Key：**

| 帳號 | 到期日 | 方案 |
|------|--------|------|
| bingo@napesone.com | **2026-02-24** | Free Trial $300 USD |
| bingo@mynet.com.tw | **2026-04-08** | Free Trial $300 USD |

**查看 API Key 狀態：**
```bash
python generate_image.py --status
```

### 使用腳本生成圖片

腳本位置：`~/.claude/skills/fanpage-article-writer/scripts/generate_image.py`

```bash
# 基本用法（一張精準配圖）
python generate_image.py "提示詞" -o "hero.png" --model imagen --aspect 16:9
```

### 可用模型

| 模型 | 參數 | 特點 | 費用 |
|------|------|------|------|
| Imagen 4 | `--model imagen` | 照片級品質（推薦） | $0.03/張 |
| Gemini 2.5 Flash | `--model gemini` | 上下文理解佳 | 免費額度 |

### 比例選項

| 比例 | 用途 |
|------|------|
| `16:9` | FB 封面、YouTube 縮圖（推薦） |
| `1:1` | Instagram 貼文 |
| `9:16` | IG 限時動態、Reels |

---

## Step 6: 引用文獻（營養/運動/健康類必須）

### 行內引用格式（每個論點下方立即附上）

```markdown
泡菜的活菌密度高達 **10⁹-10¹⁰ CFU/g**。

> 📚 **研究支持**：Park KY et al. (2014). *J Med Food*, 17(1), 6-20.
> https://pubmed.ncbi.nlm.nih.gov/24456350/
```

### 文末參考文獻總覽

```markdown
## 參考文獻總覽

1. Park KY et al. (2014). Health benefits of kimchi. *J Med Food*, 17(1), 6-20. https://pubmed.ncbi.nlm.nih.gov/24456350/

2. Jung JY et al. (2011). Metagenomic analysis of kimchi. *Appl Environ Microbiol*, 77(7), 2264-74. https://pubmed.ncbi.nlm.nih.gov/21317261/
```

---

## 完整工作流程示例

用戶請求：
```
幫我寫一篇關於韓國泡菜益生菌的粉專文章
```

執行流程：

1. **搜尋 PubMed 資料**
   - 搜尋 kimchi, probiotics, Lactobacillus
   - 記錄具體菌株名稱（如 KU200656、KFRI342）

2. **撰寫文章**
   - 菌種 → 菌株 → 功效 → 研究連結
   - 每個功效聲稱下方立即附上 PubMed 連結

3. **生成 5 個爆款標題**

4. **分析標題 Vibe，生成一張精準配圖**
   ```bash
   python generate_image.py "Extreme close-up of glistening Korean kimchi..." -o "hero.png" --model imagen --aspect 16:9
   ```

5. **文末附上參考文獻總覽**

---

## 疑難排解

### API 配額耗盡 (429 錯誤)
- 腳本會自動切換到下一個有效的 API Key

### 圖片生成失敗
- 使用英文提示詞
- 避免要求生成真實人臉或文字

### 查看 API Key 狀態
```bash
cd ~/.claude/skills/fanpage-article-writer/scripts
python generate_image.py --status
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
