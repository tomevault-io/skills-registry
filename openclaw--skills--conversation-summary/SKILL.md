---
name: conversation-summary
description: 历史会话摘要（可选），用于增量更新摘要，默认为空 Use when this capability is needed.
metadata:
  author: openclaw
---

# Conversation Summary - Agent Instructions

Use this skill to generate summaries for conversation content.

## Usage

When the user requests any of the following:
- "帮我总结一下这段对话"
- "生成会话小结"
- "对这些聊天记录做个摘要"
- "总结一下我们刚才聊了什么"

Use the `summarize_conversation` tool to call the summary API.

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| chat_list | string | Yes | JSON formatted conversation content |
| history_summary | string | No | Previous summary for incremental update |

### chat_list Format Example

```json
[
  {"role": "user", "content": "今天天气怎么样？"},
  {"role": "assistant", "content": "今天天气晴朗，气温25度。"}
]
```

## Response

The API returns JSON with:
- `code`: Status code, 0 means success
- `message`: Status message
- `data.summary`: Generated conversation summary

## Error Handling

- If the API returns a non-zero code, report the error message to the user
- If the request fails, check network connectivity
- Ensure chat_list is valid JSON format before calling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
