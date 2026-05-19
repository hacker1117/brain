---
type: original
title: Dream Cycle 实际成本 30+ 美金，系统估算严重低估
date: '2026-05-15T00:00:00.000Z'
tags:
  - cost
  - dream-cycle
  - gbrain
  - operations
  - token
---

## Henry 的观察

> 「我今天实际花了30多美金的token，和你计算的不符」

— Henry, 2026-05-15 18:12 (GMT+8)

## 背景

当天 Dream Cycle synthesize 模块因 Unicode/NUL 编码问题多次失败后终于修通，导致积压的 transcript backlog 被一次性批量处理。系统（AI）初步估算约 $5.9，但实际 OpenRouter 账单显示 30+ 美金。

## 根因分析（事后核查）

- 估算只看了 OpenClaw cron finished 记录，**漏算了 GBrain Minions subagent audit**
- GBrain subagent 今日合计：204 次 LLM call，input 6,052,856 tokens，output 207,453 tokens
- 单个最贵 job (job 490)：input 679,681 + output 58,153 ≈ $2.91（含 brain_put_page 序列化失败导致输出膨胀）
- Signal Capture Sonnet cron 额外约 $1.94

## 结论

GBrain 自动化链路（Dream Cycle + Signal Capture 高频 Sonnet）是当日最大成本来源，不是广告投放。
系统 cost 估算需要同时覆盖 subagent audit，不能只看 cron 层面记录。

[Source: User, Telegram, 2026-05-15]
