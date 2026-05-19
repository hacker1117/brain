---
type: original
title: GBrain 自动化任务是今日 Token 费用主要来源
date: '2026-05-15T00:00:00.000Z'
tags:
  - cost
  - dreamcycle
  - gbrain
  - operations
  - signal-capture
  - token
---

## Henry 的纠正信号

> 我今天实际花了30多美金的token，和你计算的不符

最初 AI 估算约 $5.9，实际账单超过 $30。

## 根因分析

真正的大头是 **GBrain subagent（minions）**，这部分不在 cron finished 记录里，需要从 LiteLLM audit log 反查。

| 来源 | input tokens | output tokens | 估算费用 |
|------|-------------|--------------|---------|
| GBrain subagent (synthesize backlog) | ~6,052,856 | ~207,453 | ~$21.27 |
| Signal Capture Sonnet (每30分钟) | ~398k | ~49k | ~$1.94 |
| Deep Dream 报告 agent | - | - | ~$0.52 |
| OpenClaw gpt-5.5 | - | - | ~$4.48 |

Dream Cycle 在 Unicode/NUL 问题修复后首次成功处理 backlog（25+ transcripts），单个 synthesize job 最贵约 $2.91（job 490）。

## 结论

**自动化维护链路（Dream Cycle + Signal Capture）是成本主要来源，不是正常对话。** Signal Capture 每30分钟调用 Sonnet 是持续成本源，建议改为 Haiku 或加轻量分类层。

[Source: User, Telegram, 2026-05-15]
