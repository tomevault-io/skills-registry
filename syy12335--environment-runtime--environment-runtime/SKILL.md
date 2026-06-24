---
name: time-range-info
description: 查询某个时间段的时效信息；需要先锚定当前时间，再检索外部信息并输出证据化结论。 Use when this capability is needed.
metadata:
  author: syy12335
---
# 时间段信息查询（通用时效资讯）

本文件只用于让主 `executor` 理解这个 `pyskill` 的触发语义与 worker 输入接口。

真实执行入口是 `allowed-tools` 中声明的 `web_search`。委派后会启动后台 worker graph；时间锚定、检索、精炼、验证、回答、trace 和结果格式都由 `web_search` 内部处理。

主 `executor` 不需要理解或复述 worker graph 的内部流程。

## 工具接口

委派形式：

```json
{"action_kind":"delegate_skill","skill_name":"time-range-info","tool_name":"web_search","input":{"query":"...","limit":3},"reason":"..."}
```

`input` 是 JSON object：

- `query`：必填，表达用户检索意图；可以包含相对时间表达，以及用户关心的地域、对象或主题词。
- `limit`：可选，默认 `3`。
- 默认 `limit=3`；主题很宽时可用 `limit=5`。

示例：

```json
{"query":"昨天 北京 重大事件 新闻","limit":3}
```

## 边界

- 不读取 `docs/graph_flow.md`、`config/retrieval_policy.yaml`、`training/*` 等 worker 内部文件。
- 不基于本文件编造答案；答案由 worker 回填结果提供。
- 输出 `delegate_skill` 后，当前 executor 回合只需要进入 `running`。

---
> Source: [syy12335/Environment-Runtime](https://github.com/syy12335/Environment-Runtime) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
