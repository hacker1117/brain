---
type: original
title: GBrain autopilot patterns phase：缺少幂等保护导致高频 Sonnet 重复调用
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - bug-fix
  - cost-control
  - gbrain
  - patterns
---

## 问题发现

今天发现 GBrain autopilot 在一天内消耗了 30+ 美金的 Sonnet 费用。排查发现根本原因在于 `patterns` phase 缺少幂等保护：

> "它不是因为任务没执行完反复 retry，而是因为 `patterns` 缺少「已处理同一批 reflections」的去重/冷却机制，导致每 5 分钟重新分析同一批内容。"

每次 autopilot-cycle（默认 300s 间隔）都会：
1. 收集最近 30 天 reflections（同一批，没有变化）
2. 数量 >= 3 就提交 Sonnet subagent
3. 没有 cooldown，没有 reflection-set hash
4. 即使判断"没有新 pattern 要写"，本次仍然消耗 10万+ input tokens
5. 下一个 cycle 5 分钟后，重复同样流程

## Henry 的判断

Henry 的直觉是：**GBrain 设计者不会设计这么浪费 token 的方式，更可能是 bug**。这个判断是对的。

> "GBrain 的设计方不会设计这么浪费 token 的方式。那么我们再做一个检查，就是说 autopilot 的 sub agent 消耗了这么多的 token。是不是因为某些 bug 的存在？就比如说它认为某一个任务一直没执行完，所以反复地运行。或者说是，这个 subagent 应该已经处理完了某一些内容应该标记上，然后就不再处理了，但它还在反复地处理。"

对比 `synthesize` phase 有完整的保护机制：
- `dream.synthesize.last_completion_ts`
- 12h cooldown
- transcript content hash
- idempotency key per file

而 `patterns` phase 完全没有类似机制，这是设计遗漏/bug。

## 修复方案

给 `patterns` 加了两层保护：

1. **12h cooldown**
   - 配置：`dream.patterns.cooldown_hours`（默认 12）
   - 状态：`dream.patterns.last_completion_ts`

2. **reflection-set hash**
   - 状态：`dream.patterns.last_reflection_set_hash`
   - 如果最近 reflections 集合没变化 → 直接 `skipped: already_processed`

修复后验证：autopilot-cycle #422 起 `patterns` 均为 `skipped: cooldown_active`，Sonnet subagent 调用数降至 0。

## 成本影响

- 修复前：autopilot 每 5 分钟触发，每天理论 288 次 patterns subagent，$30+/天
- 修复后：每 12 小时一次（有新 reflections 才真正执行），预计降至 $1-3/天

commit: `9518968 Throttle recurring pattern detection`

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
