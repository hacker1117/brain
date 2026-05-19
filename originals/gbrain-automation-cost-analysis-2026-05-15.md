---
type: original
title: GBrain 自动化成本分析与优化方向（2026-05-15）
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - openrouter
  - signal-capture
---

## 背景

2026-05-15，Henry 发现当日 OpenRouter/Sonnet 实际花费超过 $30，远超初步估算的 $5.9。

## 根本原因分析（Henry 的判断）

> "我今天实际花了30多美金的token，和你计算的不符"

初步估算只统计了 cron run history 里的 finished 记录，漏掉了 GBrain Minions subagent 的实际调用：

- **GBrain subagent 当日合计**：204 次 LLM call，input 6,052,856 tokens + output 207,453 tokens → 约 $21.27
- **主要来源**：Dream Cycle 的 synthesize backlog 批处理（修复 Unicode/NUL 后，一次性处理了积压 transcript）
- **单个最贵 job**：job 490，input 679,681 + output 58,153 → 约 $2.91（含工具调用失败导致 output 放大）
- **Signal Capture Sonnet cron**：约 $1.94（每 30 分钟跑一次，高频持续成本）
- **Dream Cycle 报告 agent**：约 $0.52

## 核心教训

1. 成本估算必须包含 GBrain Minions subagent audit，不能只看 cron run history
2. Signal Capture 跑 Sonnet 过重，是持续成本源
3. Dream Cycle synthesize 的工具调用空参数错误会导致循环烧 token（`put_page {}` → TypeError → 模型继续重试）

## 优化方向

- Signal Capture 改 Haiku，或先轻量分类再决定是否调 Sonnet
- Dream Cycle 每个 synthesize child job 限制 max_turns（已改 30→8）
- Dream Cycle 改为每天只跑 02:00 一次（已按 Henry 指示调整）
- 下次大改之前先做 preflight：统计 transcript 数量 + 预估 child jobs，确认规模后再启动

[Source: User, Telegram, 2026-05-15]
