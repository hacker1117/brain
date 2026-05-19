---
type: original
title: GBrain 自动化链路 Token 费用事件 — 2026-05-15
date: '2026-05-15T00:00:00.000Z'
tags:
  - budget
  - cost
  - dreamcycle
  - gbrain
  - incident
  - openrouter
  - token
---

Henry 发现 2026-05-15 实际花费 **30+ 美金** token，与我初步估算的 $5.9 严重不符。

## Henry 原话

> 我今天实际花了30多美金的token，和你计算的不符

## 根因分析（事后确认）

真正烧钱的不是正常聊天，而是 **GBrain 自动化维护链路**：

1. **Deep Dream Cycle synthesize backlog 批处理**（最大头）
   - GBrain subagent 204 次 LLM call
   - Input: 6,052,856 tokens；Output: 207,453 tokens
   - 按 Sonnet 定价约 $21.27
   - 修完 Unicode/NUL 问题后，Dream Cycle 开始处理积压的 transcript，集中在 15:15–16:00

2. **Signal Capture Sonnet 高频 cron**：约 $1.94
3. **Deep Dream 报告 agent 多次重试**：约 $0.52
4. **OpenClaw/gpt-5.5 侧**：约 $4.48

## 关键教训

- 初步估算只看了 cron finished 记录，**漏掉了 GBrain Minions subagent**
- Dream Cycle 修复后一次性处理 backlog → 费用集中爆发
- Signal Capture 每 30 分钟跑 Sonnet → 持续成本源，需优化

## 后续行动

- 下一次 Dream Cycle 运行前要确认 transcript 数量和预计费用
- 考虑把 Signal Capture 从 Sonnet 改 Haiku，或做轻量分类前置
- Dream Cycle 加成本保护/transcript 数量限制

[Source: User, Telegram/OpenClaw Transcript, 2026-05-15]
