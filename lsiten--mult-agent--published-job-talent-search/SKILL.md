---
name: published-job-talent-search
description: Use when the user wants to find talent for job postings stored in the RecruitAI SQLite database, especially by selecting job_postings rows whose status is 已发布, 待发布, or 已完成, exporting job and active weight JSON files, then running existing Liepin or LinkedIn search and candidate scoring skills. Trigger when the user says to search talent from SQL/SQLite/database published jobs, run scripts for published岗位需求, or batch-process released job requirements into candidate search tasks.
metadata:
  author: lsiten
---

# Published Job Talent Search

## Overview

这个 skill 负责把 RecruitAI SQLite 里的已发布岗位变成可执行的找人任务。

它只做调度和准备，不重新定义页面抓取或候选人评分规则：

- 从 `job_postings` 读取已发布岗位
- 导出每个岗位的 `job.json`
- 读取同库 `job_posting_scores` 的 active 权重，导出 `job.score.json`
- 初始化猎聘/LinkedIn 搜人上下文
- 后续抓取、保存候选人和评分复用 `liepin-resume-search`、`linkedin-people-search`、`candidate-match-score`
- 最终候选人必须写入同一 RecruitAI SQLite 的 `recruit_candidates`

## Status Rule

默认把以下状态视为可以开始找人的“已发布岗位”：

- `已发布`：旧版或用户口语里的发布态
- `待发布`：岗位已完成权重确认，等待/准备发布
- `已完成`：RecruitAI API 会把旧 `已发布` 归一化成这个状态

如果用户明确要求只处理 SQL 中 `status = 已发布`，运行脚本时传：

```bash
python3 "<skill_dir>/scripts/prepare_published_jobs.py" --status 已发布
```

## Workflow

### 1. Prepare Published Jobs

先运行本 skill 的脚本：

```bash
python3 "<published_job_talent_search_skill_dir>/scripts/prepare_published_jobs.py"
```

默认数据库路径优先级：

1. 用户指定的 `--db-path`
2. `HERMES_RECRUIT_DB_PATH`
3. `$HERMES_HOME/job_postings.sqlite`
4. 当前工作目录的 `job_postings.sqlite`

常用参数：

```bash
python3 "<skill_dir>/scripts/prepare_published_jobs.py" \
  --db-path /absolute/path/to/job_postings.sqlite \
  --status 已发布 \
  --limit 10 \
  --channels liepin,linkedin
```

脚本会输出一个 JSON 摘要，并在 `data/published-job-talent-search/` 下为每个岗位创建任务目录。

### 2. Check Scores

每个岗位必须有 active `job.score.json` 才能进入平台搜人。

如果 manifest 中某个岗位 `needs_score = true`：

1. 用 `job-posting-score-sqlite` 给该 `job_postings.id` 生成正式权重
2. 写回同一个 SQLite 的 `job_posting_scores`
3. 重新运行 `prepare_published_jobs.py`

不要临时编造权重文件，也不要跳过评分权重直接搜人。

### 3. Run Platform Search

根据用户指定的平台选择后续 skill：

- 猎聘：使用 `liepin-resume-search`
- LinkedIn：使用 `linkedin-people-search`
- 两个平台都跑：先猎聘，后 LinkedIn，或者按用户指定顺序

对于脚本已经初始化好的任务，读取每个岗位目录下对应平台的 `search-context.json` 和 `search-master.json`，然后继续执行平台 skill 的页面操作、候选人抓取和评分。

### 4. Score And Maintain Master Tables

每抓到一位候选人，必须立即调用 **`candidate-match-score`** 生成确定性 `*_score.json`，再更新平台 `search-master.json`。

若用户需要基于 **`job_posting_scores` 权重 JSON** 的 **LLM 语义评估**（非规则脚本），额外调用 **`recruit-ai-candidate-match-score`**，生成 **`*_ai_score.json`**；两类评分文件并存，勿互相覆盖。

不要只保存候选人列表摘要。每个候选人至少要有：

- 独立候选人 JSON
- 独立评分 JSON（确定性 `*_score.json`；按需追加 AI `*_ai_score.json`）
- `search-master.json` 中的记录

### 5. Write Candidates To SQLite

平台输出文件只是中间产物。每个有效候选人完成评分后，必须写入 RecruitAI SQLite 的 `recruit_candidates` 表；可复用 `liepin-resume-search` / `linkedin-people-search` 的 SQLite 写入步骤，或直接调用 RecruitAI API `POST /api/recruit/candidates`。

写入字段必须统一：`candidate_uid`、`name`、`job_posting_id`、`match_score`、`match_level`、`match_reason`、`source_type`、`source_platform`、`source_url`、`source_json`、`raw_json`。`job_posting_id` 必须来自当前处理的真实 `job_postings.id`，保存后会同步写 `recruit_candidate_matches`；需求管理里每个岗位的人才预览只显示该岗位关联的人才。

最终报告必须包含：

- SQLite `database_path`
- `table = recruit_candidates`
- `inserted`
- `updated`
- `ids`

如果只生成了 `search-master.json`、候选人 JSON 或评分 JSON，不能说“已入库”；必须继续补写 SQLite 或明确报告阻塞原因。

### 6. Report Back

完成或中断时，用中文汇报：

- 读取的数据库路径
- 命中的已发布岗位数量
- 每个岗位的 `job_postings.id`、公司、岗位名、状态
- 是否已有 active 权重
- 初始化了哪些平台任务目录
- 已抓取/已评分候选人数
- 写入 `recruit_candidates` 的 inserted/updated/ids
- 阻塞项，例如登录态、验证码、平台权限、缺少权重

## Output Layout

默认布局：

```text
data/published-job-talent-search/
  manifest.json
  <job-slug>-job<id>/
    job.json
    job.score.json
    run-manifest.json
    liepin/
      <job-slug>/
        search-context.json
        search-master.json
    linkedin/
      <job-slug>/
        search-context.json
        search-master.json
```

详细字段和状态兼容规则见 [references/sqlite-published-job-workflow.md](references/sqlite-published-job-workflow.md)。

## Quality Rules

- 只使用 SQLite 中真实存在的岗位和权重，不要虚构岗位需求。
- 没有 active 权重时，先补权重再搜人。
- 不要覆盖原始 SQLite 数据；本 skill 的脚本只读数据库并导出任务文件。
- 允许向同一 SQLite 的 `recruit_candidates` 新增或更新候选人；这不属于覆盖岗位原始数据。
- 不要把 `待完善`、`待评分`、`已暂停` 默认纳入找人任务，除非用户明确指定。
- 遇到猎聘/LinkedIn 登录、验证码、限流或权限问题时，记录阻塞点，不要编造候选人。
- 后续平台搜人必须复用现有 skill，不要在本 skill 里写第二套浏览器自动化或评分逻辑。

---
> Source: [lsiten/mult-agent](https://github.com/lsiten/mult-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
