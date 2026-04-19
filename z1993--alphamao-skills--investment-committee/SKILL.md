---
name: investment-committee
description: 组建高级投资委员会，由三位传奇投资人风格的虚拟专家（Buffett, Wood, Druckenmiller）进行独立多轮对抗辩论。采用物理隔离的 Gemini API 调用实现真正独立思考，并通过投票形成最终决议。Use when evaluating investment decisions, reviewing stock research reports, or seeking multi-perspective analysis on public companies. Use when this capability is needed.
metadata:
  author: z1993
---

# Investment Committee (Multi-Agent Adversarial V3)

启动一个由三位顶级投资人风格组成的虚拟委员会，对目标公司进行严格的独立审查、对抗式辩论和投票决议。

---

## ⚔️ 核心设计原则

1.  **物理隔离**: 每个 Agent 是独立的 Gemini API 调用，拥有完整的 Persona 系统提示。
2.  **深度人设**: 通用化投资哲学框架，不依赖特定案例。
3.  **数据注入**: 自动抓取实时宏观数据（利率、汇率、VIX），并采用前向填充 (`ffill`) 处理节假日/缺失数据，确保德肯米勒始终获得有效情报。
4.  **智能重试**: 内置指数退避 (Exponential Backoff) 机制，自动处理 API 429 限流错误。
5.  **决议机制**: 辩论结束 --> 自动提取投票 --> 主席 Agent 形成决议。

---

## 👥 委员会成员

| 角色 | 投资哲学内核 | 数据注入 |
| :--- | :--- | :--- |
| **巴菲特** | "生意"思维、护城河、安全边际 | 标准研报 |
| **木头姐** | 莱特定律、S曲线拐点、技术融合 | 标准研报 |
| **德肯米勒** | 流动性为王、价格行为、不对称赔率 | **实时宏观快照** (美债/美元/VIX) |

详细人设见: `references/personas/` 目录。

---

## 🔄 执行流程

### Pre-Flight: 数据抓取
脚本自动从 Yahoo Finance 抓取：
- 10年期美债收益率 (`^TNX`)
- 美元指数 (`DX-Y.NYB`)
- 恐慌指数 (`^VIX`)
- 标普/纳指趋势 (`SPY`, `QQQ`)

### Phase 1: 独立初评 (Independent Review)
每位专家阅读研报（德肯米勒额外获得宏观数据），给出独立判断。

### Phase 2: 对抗辩论 (Adversarial Debate)
多轮辩论，针对性反驳，并更新立场。

### Phase 3: 投票决议 (Voting & Decision)
- 提取每位专家的投票（买入/拒绝/观望）和置信度。
- 结合宏观背景生成《投资委员会最终决议》。

---

## 🛠️ 技术栈

*   **LLM**: Google Gemini 2.0 Flash (`google-genai`)
*   **Data**: Yahoo Finance (`yfinance`)
*   **Env**: 支持 HTTP 代理

---

## 🚀 使用方法

### 1. 安装依赖
```bash
pip install -r requirements.txt
```

### 2. 设置环境
```bash
# Windows PowerShell
$env:GEMINI_API_KEY='<YOUR_API_KEY>'  # 替换为你的 Gemini API Key

# 可选代理（如使用 Clash/V2Ray 等）
$env:HTTP_PROXY='http://127.0.0.1:<PORT>'  # 替换为你的代理端口
$env:HTTPS_PROXY='http://127.0.0.1:<PORT>'
```

> **获取 API Key**: 访问 [Google AI Studio](https://aistudio.google.com/app/apikey) 创建 API Key

### 3. 运行
```bash
python scripts/run_committee.py <path_to_report.md> --rounds 3 --output ./output
```

---

## 📁 Skill 目录结构

```
investment-committee/
├── SKILL.md                      # 本文件 (架构说明)
├── TROUBLESHOOTING.md            # 问题排查
├── requirements.txt              # 依赖 (google-genai, yfinance)
├── scripts/
│   └── run_committee.py          # 核心执行脚本 (包含数据抓取逻辑)
└── references/
    └── personas/                 # 通用化人设提示词
        ├── buffett.md
        ├── wood.md
        └── druckenmiller.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z1993) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
