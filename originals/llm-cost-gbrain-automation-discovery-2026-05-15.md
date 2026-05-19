---
type: original
title: GBrain 自动化链路成本暴露：实际花费 $30+ vs 估算 $5.9
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - lesson
  - signal-capture
  - token-spend
---

# GBrain 自动化链路成本暴露

## Henry 的原话

「我今天实际花了30多美金的token，和你计算的不符」

「今天的投放花费费用还是很多，你能帮忙再确认一下，这个花费是来自于哪里吗？」

## 发现过程

Henry 发现实际 token 花费 **$30+**，与 AI 估算的 $5.9 严重不符，主动追问根因。

## 根因分析

初步估算只看了 cron finished 记录，**漏算了 GBrain Minions subagent audit**：

| 来源 | 估算 | 实际占比 |
|------|------|---------|
| GBrain subagent（204次 LLM call） | 漏算 | ~$21.27（6M input + 207k output @ Sonnet 定价） |
| Signal Capture Sonnet cron | $1.94 | 持续成本源 |
| Deep Dream 报告 agent | $0.52 | 多次重试放大 |
| OpenClaw / gpt-5.5 侧 | $4.48 | — |

**最大账源：Deep Dream Cycle synthesize 处理 backlog**（job 480–501，修完 Unicode/NUL 后首次成功批处理 25+ transcripts）

## 关键教训

1. 本地 cron log 看到的数字 ≠ 真实账单；GBrain subagent 调用不完整体现在 cron usage 里
2. Signal Capture 每30分钟用 Sonnet 是持续高成本源，建议改 Haiku 或"先轻量分类"
3. Dream Cycle 修复后首次 backlog 处理成本会集中爆发，需预期

## 后续动作

- 把 Signal Capture 从 Sonnet 改 Haiku 或分级处理
- 建立可靠的全链路 token 统计（不只看 cron，要看 minion audit）

[Source: User, Telegram DM, OpenClaw Transcript, 2026-05-15]
