---
name: context-optimize
description: 提高长时间或复杂会话的上下文效率和缓存稳定性。 Use when this capability is needed.
metadata:
  author: sswym
---

## 目的

当上下文快速增长或缓存一致性下降时，使用此技能。

## 触发条件

- 对话变得冗长且重复
- 模型失去线程连贯性
- 出现频繁的提示重启或不稳定行为

## 工作流程

1. 识别重复、过时或低价值的上下文。
2. 检测可能的缓存破坏者（指令变更、不稳定的前缀）。
3. 提出缓存安全的压缩和消息卫生规则。
4. 为后续轮次生成可操作的操作清单。

## 输出模板

```markdown
## 上下文健康状况
- ...

## 缓存风险
- ...

## 优化操作
1. ...
2. ...
3. ...

## 会话规则
- ...
```

## 注意事项

- 在会话期间保持稳定指令不变。
- 倾向于使用摘要而不是重新发送原始大块内容。

---
> Source: [sswym/oh-my-agent](https://github.com/sswym/oh-my-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
