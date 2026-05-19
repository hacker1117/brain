---
type: original
title: Dream Cycle Token 成本根因分析（2026-05-15）
date: '2026-05-15T00:00:00.000Z'
tags:
  - dream-cycle
  - engineering
  - gbrain
  - lessons-learned
  - token-cost
---

# Dream Cycle Token 成本根因分析

今天 GBrain 实际花了 $30+ 的 token，和初步估算的 $5.9 严重不符。

## 真正的大头账源

排查之后发现，最贵的不是 cron finished 记录里的任务，而是 **GBrain Minions subagent audit**：

- GBrain subagent 今日合计：204 次 LLM call
- input：6,052,856 tokens；output：207,453 tokens  
- 粗估 $21.27（Sonnet $3/M in + $15/M out）

主要来自 **15:15 左右 Deep Dream Cycle 的 synthesize backlog 批处理**：
- jobs 480–501，一口气处理了大量积压 transcript
- 这是修完 Unicode/NUL bug 后 synthesize 终于跑通，导致积压全部释放
- 单个最贵 job 490：input 679,681 + output 58,153，约 $2.91
- 其中一次 `brain_put_page` 序列化失败导致输出异常放大

## 根因链条

1. Synthesis 阶段因 Unicode 问题一直 fail → 无输出 → 积压 transcript 堆积
2. 修复后，积压 transcript 一次性全部进入 synthesize → 大量子 agent 并发
3. 每个 subagent 的 token 使用不体现在 cron usage 里 → 初步估算严重低估

## 经验总结

- **不能只看 cron run history**，必须查 GBrain Minions subagent audit
- Dream Cycle 修复后应先 dry-run 确认 transcript 数量，再决定是否全量跑
- `put_page {}` 空参数导致的循环重试会显著放大 output token
- Signal Capture 高频 Sonnet（每 30 分钟）也是持续成本源，建议改 Haiku

[Source: User, Telegram, 2026-05-15]
