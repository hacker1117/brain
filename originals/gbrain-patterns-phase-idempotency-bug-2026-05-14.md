---
type: original
title: GBrain patterns phase 缺少幂等保护的 bug 定性
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - bug
  - cost-control
  - gbrain
  - idempotency
  - patterns
---

# GBrain patterns phase 缺少幂等保护的 bug 定性

Henry 的判断（确认 `patterns` 重复消耗是 bug，不是设计）：

> 我依然有一个假设，就是 GPT brain 的设计方不会设计这么浪费 token 的方式……我觉得我更倾向于认为它是一个 bug 造成的。

## 诊断结论

**真正反复烧 Sonnet 的是 `patterns` phase，不是 `synthesize`。**

- `synthesize` 有保护：`last_completion_ts` + 12h cooldown + transcript content hash + idempotency key
- `patterns` 没有类似保护：
  - 每次 autopilot-cycle 都收集最近 30 天 reflections
  - 只要 `reflections >= 3`，就提交 Sonnet subagent
  - 没有 cooldown，没有 reflection-set hash，没有"已处理"标记
  - 即使模型最后判断"无需更新"，也已花掉 10 万+ input tokens

证据：最近一次 patterns job 消耗约 `input: 165,397 tokens, output: 2,769 tokens`，只分析了同一批 4 个 reflections，模型输出"已有 13 个 pattern 已覆盖，无需更新"。

## Henry 的决策：最小修法

> 我想了解一下这个所谓的 cool down 的机制是怎么实现的呢？我是不是应该给这个 patterns 也加上 cool down 就结束了？

最终确认修法：**cooldown + reflection-set hash**

- 仅加 cooldown（24h）：能止血，够快
- 加 cooldown + hash：才是根治（如果 reflections 集合没变化，直接 skip）

## 关联

- [[originals/gbrain-autopilot-repeated-subagent-bug-hypothesis-2026-05-14]]
- [[originals/gbrain-autopilot-phase-split-decision-2026-05-14]]
- [[originals/patterns-cooldown-as-minimal-fix-question-2026-05-14]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
