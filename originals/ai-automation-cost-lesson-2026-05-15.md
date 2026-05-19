---
type: original
title: AI 自动化成本教训 — GBrain 一天烧了 30+ 美金
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - lesson
  - openrouter
---

# AI 自动化成本教训 — GBrain 一天烧了 30+ 美金

## 事件

2026-05-15，Henry 发现今天 token 费用异常高，实际花费超过 **$30**，和 AI 估算的 $5.9 严重不符。

## 根因

1. **Dream Cycle synthesize backlog 批处理**：修完 Unicode/NUL 问题后，Dream Cycle 终于能正常运行，结果一口气处理了积压的大量 transcript，单次 synthesize 消耗 6M+ tokens。
2. **GBrain subagent token 消耗未被 cron 记录统计**：正常 cron run history 只记录 finished job，subagent 派出去的 204 次 LLM call 没有出现在明显的成本汇总里，导致估算严重低估。
3. **Signal Capture 每 30 分钟跑 Sonnet**：持续成本源，今天已跑多轮。
4. **`put_page {}` 空参数循环**：某些 synthesize subagent 因工具调用空参数失败后不立即退出，而是把错误喂回模型继续尝试，放大了消耗。

## 关键观察

- 自动化链路的成本不透明度很高：cron 记录 ≠ 实际 LLM 费用
- Backlog 释放时的批量处理是最大的成本事件
- 同一个调试循环（修复 + 多次 rerun）也会累积意外费用

## Henry 原话

> "我今天实际花了30多美金的token，和你计算的不符"

## 后续决策

- Dream Cycle 调整为每天只在凌晨 2 点执行一次（原来是 2 次）
- 加入工具调用空参数强校验，防止循环烧 token
- synthesize 子任务 max_turns 从 30 降为 8，单 chunk timeout 从 30min 降为 12min

[Source: User, Telegram, 2026-05-15]
