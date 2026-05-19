---
type: original
title: GBrain 今日花费分析 — 实际 $30+ vs 估算 $5.9 的差距根因
date: '2026-05-15T00:00:00.000Z'
tags:
  - cost
  - dream-cycle
  - gbrain
  - infrastructure
  - signal-capture
---

## 事实

Henry 确认：2026-05-15 实际 OpenRouter token 花费 **$30+**。
助手最初估算为 ~$5.9，严重低估。

## 根因

初次估算**只看了 cron run history 里可见的 finished 记录**，没有算 GBrain Minions subagent audit。

真正花钱的来源：

1. **GBrain subagent 今日合计（重新核对）**
   - 204 次 LLM call
   - input：6,052,856 tokens
   - output：207,453 tokens
   - 按 Sonnet $3/M input + $15/M output 粗算：**~$21.27**
   - 主要来源：Dream Cycle synthesize backlog 批处理（job 480–501）

2. **Signal Capture Sonnet cron（每 30 分钟）**
   - 粗估 **~$1.94**（持续成本源）

3. **Deep Dream Cycle 报告 agent 多次重试**
   - 调试期间反复失败、重试
   - 粗估 **~$0.52**

4. **gpt-5.5 侧成本**：约 ~$4.48

## 关键教训

- GBrain 的 subagent 花费不体现在 cron usage 里，必须从 Minions audit 反查
- Signal Capture 用 Sonnet 是**持续成本源**，建议降为 Haiku 或"先分类、有信号再 Sonnet 写入"
- Dream Cycle synthesize 处理 backlog 时会集中爆发大量 token，需要成本保护机制

> Henry 原话：「我今天实际花了30多美金的token，和你计算的不符」

[Source: User, Telegram, 2026-05-15]
