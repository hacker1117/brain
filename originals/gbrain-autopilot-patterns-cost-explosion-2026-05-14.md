---
type: original
title: GBrain Autopilot Patterns Phase 成本爆炸与修复
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - gbrain
  - infrastructure
  - patterns
---

# GBrain Autopilot Patterns Phase 成本爆炸与修复

## 问题发现

Henry 今天发现 Sonnet 模型费用单日超过 30 美元，明显超过预期（每天几美元量级）。

> "我期望的或者说我设想中的一个费用应该是比这个低一个数量级的，每天应该在几美金左右。并且，G-Brain 的创造者 Gary Tan 他自己是接触着比我更多的信息，管理着比我更多的项目，他也没有用这么超额的这个 token 费用。"

## 根因分析

**主要元凶：GBrain autopilot 的 `patterns` phase 缺少幂等保护**

- `autopilot` 默认 interval = 300 秒（5 分钟）
- `autopilot-cycle` 与 `gbrain dream` 共用同一套完整 phase set（GBrain 官方设计）
- `synthesize` phase 有 12h cooldown 保护，但 `patterns` phase **没有任何去重/冷却机制**
- 每次 autopilot-cycle，只要最近 reflections 数量 >= 3，就无条件提交 Sonnet subagent
- 即使最终分析结论是"已有 pattern 已覆盖，无需更新"，也会烧掉 10 万+ input tokens
- 单次消耗约 $0.73 ~ $1.18，每天 288 次 = 最高 $200+（实际今天烧了约 $33）

今日数据统计（修复前）：
- GBrain autopilot subagent：66 个 job，353 次 LLM call
- Sonnet input：9,496,164 tokens
- Sonnet output：313,123 tokens
- 估算费用：**$33.19**

## Henry 的关键判断

> "我依然有一个假设，就是 GPT brain 的设计方不会设计这么浪费 token 的方式。那么我们再做一个检查，就是说 autopilot 的 sub agent 消耗了这么多的 token。是不是因为某些 bug 的存在？就比如说它认为某一个任务一直没执行完，所以反复地运行。"

这个判断是正确的。`patterns` 缺失幂等保护属于设计缺陷（不是刻意的高频运行设计）。

## 修复方案

两层保护：

**1. 12h Cooldown**
- 新配置：`dream.patterns.cooldown_hours`（默认 12）
- 状态键：`dream.patterns.last_completion_ts`
- 每次 patterns 跑前检查，cooldown 内直接 skip

**2. Reflection Set Hash**
- 新状态键：`dream.patterns.last_reflection_set_hash`
- 计算最近 reflections 集合的 hash
- 若 hash 与上次一致，直接 `skipped: already_processed`
- 只有 hash 变化 + cooldown 过期，才真正运行 Sonnet

已提交：`9518968 Throttle recurring pattern detection`

## 验证结果

修复后 autopilot-cycle #422 ~ #431：
- `patterns` = `skipped: cooldown_active`
- `synthesize` = `skipped: cooldown_active`
- subagent 提交 = 0
- LLM call = 0
- 单次 cycle 耗时从 90~200s 降到 31~34s

## 关联架构决策

修复后明确了「autopilot vs nightly Dream」的职责分离原则：参见 [[concepts/gbrain-autopilot-light-vs-nightly-dream-strategy]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
