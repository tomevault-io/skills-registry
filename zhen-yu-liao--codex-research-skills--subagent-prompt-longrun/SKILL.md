---
name: subagent-prompt-longrun
description: 為 Codex 產生長時間或 one-shot subagent 開發 prompt。用於使用者要求先閱讀目前 repo、理解 `Spec.md` / `AGENTS.md` / entrypoints / tests / build scripts 與可用 skills，再依使用者語言輸出一份可直接貼上的 prompt，驅動多個 subagents 完成完整開發、整合、測試與 e2e 交付。 Use when this capability is needed.
metadata:
  author: Zhen-Yu-Liao
---

# 長任務 Subagent Prompt

先掃描專案事實，再輸出一份可直接貼上的 prompt。不要直接套用空模板，也不要把 `Spec.md` 與 `AGENTS.md` 重寫一遍。

## 典型使用者說法

- 「幫我寫一個 one-shot prompt，讓 Codex 用 subagents 把這個專案完整做完。」
- 「先看我的 repo 跟 spec，再幫我產一份長任務開發 prompt。」
- 「我要去睡覺了，幫我寫一份可直接丟給 Codex 的 long-run subagent prompt。」

## 工作流程

1. 先建立 repo 事實基線。
   - 讀 `Spec.md`、`AGENTS.md`、assignment/需求文件、manifest、entrypoints、tests、build scripts。
   - 檢查 `.codex/config.toml`、`.codex/agents/`、可用 package manager、測試框架、e2e 能力。
   - 判斷目前是空骨架、半成品，還是已有穩定架構。
   - 只把已確認存在的檔案、資料夾、腳本與工具寫進 prompt；未確認前不要假設存在。
2. 再篩選最小必要 skills。
   - 只從當前 session 已可用的 skills 中挑選。
   - 優先考慮 `planner`、`ai-engineer`、`package-manager-detector`、`build-error-resolver`、`systematic-debugging`、`e2e-tester`、`code-reviewer`、`doc-maintainer`。
   - 若某 skill 不存在或不適用，直接略過，不要虛構。
3. 只在高影響未知無法從 repo 推得時發問。
   - 最多問 1 到 3 個問題。
   - 若可合理假設，優先假設並在 prompt 內固化。
4. 再撰寫 prompt。
   - 開頭插入挑中的 skill links，使用當前 session 真實可用的絕對路徑。
   - 引用 `Spec.md` 與 `AGENTS.md` 的角色分工，不重複抄寫穩定規則。
   - 讓 prompt 明確要求使用 subagents，且子任務必須是 bounded jobs。
5. 只輸出 prompt。
   - 不附分析、說明、檢查清單或額外提案。

## Prompt 必備內容

- 明確說明這是 one-shot 或長時間完整開發任務。
- 明確要求主 agent 先讀 repo，再啟動 subagents。
- 若 `.codex/agents/` 已存在 custom agents，要求優先使用；否則使用預設 subagents。
- 明確要求子任務寫入範圍不重疊，且不得覆寫或回滾其他 agent 的工作。
- 明確要求每輪平行工作後 `wait for all`，由主 agent 整合、補漏、解衝突。
- 明確要求 subagent 回傳固定格式：
  - `ownership`
  - `changed files`
  - `commands run`
  - `tests run`
  - `open risks / blockers`
- 明確要求保留既有 scaffold 與 repo 慣例，不為理想架構推倒重來。
- 明確要求不要修改真實 `.env`，優先處理 `.env.example`、server-side config loading、provider abstraction。
- 明確要求列出實際執行的 build / test / e2e 指令與 pass/fail。
- 明確要求若遇到 blocker，仍推進到最大可交付狀態，並精確描述影響範圍。

## 技能挑選規則

- 只有在任務明確匹配時才把 skill 放進 prompt。
- 不要把 meta-skills 或與當前 repo 無關的 skills 一起塞進 prompt。
- 若 repo 有明確前後端或 AI/RAG 特徵，再加入對應技能；否則保持最小集合。
- 若 prompt 已引用 `Spec.md` / `AGENTS.md`，不要再用 skill 重複承載同一層規則。

## 輸出品質標準

- 使用使用者要求的語言輸出 prompt；若使用者沒有指定語言，跟隨目前對話語言。必要時保留 English 名詞、檔名、指令與 skill 名稱。
- 讓 prompt 可以直接貼進 Codex 使用，不需要二次整理。
- 依目前 repo 事實改寫 prompt，不要把任何骨架、技術棧或測試工具寫死成通用模板。
- 只引用已確認存在的路徑、命令、測試與框架；不要把常見專案結構當成既有事實。
- 讓 prompt 偏向 orchestration contract，而不是再堆一份需求條列。

## 不要做的事

- 不要直接實作專案。
- 不要生成 `.codex/agents`、rules、README 或其他配套檔。
- 不要把所有看到的 skills 都列進 prompt。
- 不要在 prompt 裡要求模糊的「幫我做一些後端」或「看情況處理」。

---
> Source: [Zhen-Yu-Liao/codex-research-skills](https://github.com/Zhen-Yu-Liao/codex-research-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
