---
type: original
title: GBrain 自动任务实际费用远超估算——根因复盘
date: '2026-05-15T00:00:00.000Z'
tags:
  - cost
  - dream-cycle
  - gbrain
  - lesson
  - signal-capture
---

# GBrain 自动任务实际费用远超估算——根因复盘

## 事件

2026-05-15，Henry 指出当天实际 OpenRouter token 花费约 **$30+**，与初步估算 **$5.9** 严重不符。

Henry 原话：
> 我今天实际花了30多美金的token，和你计算的不符

## 根因分析

初步估算只查了 cron finished 记录，漏掉了 GBrain Minions 派出的 subagent。真实大头来源：

- **GBrain synthesize backlog 批处理**：Debug 修完 Unicode/NUL 问题后，Dream Cycle 终于能正常跑，15:15 左右一口气处理了大量 transcript，204 次 LLM call，约 600 万 input tokens。
- 其中 job 490 因 `brain_put_page` 工具调用空参数触发循环重试，把输出 token 放大到约 58k。
- Signal Capture 高频 + Sonnet 也贡献了约 $2。

## 教训

1. **估算成本必须包含 GBrain Minion subagent audit**，不能只看 cron run history。
2. Dream Cycle 修 bug 后首次跑会清 backlog，费用与"正常单次"不可比。
3. 工具调用空参数循环是高成本风险点，已增加 `put_page` 入口强校验 + max_turns 从 30 降到 8。

## 已有行动

- Dream Cycle 改为只每天凌晨 2 点跑一次
- synthesize subagent max_turns 降至 8，单 chunk timeout 12min
- `put_page` 增加空参数强校验

[Source: User, Telegram, 2026-05-15]
