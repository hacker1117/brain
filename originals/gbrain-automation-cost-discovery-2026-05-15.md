---
type: original
title: GBrain 自动化成本超支发现与决策 (2026-05-15)
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - signal-capture
---

# GBrain 自动化成本超支发现与决策

Henry 在 2026-05-15 调试 Dream Cycle 期间，发现当天 OpenRouter/Sonnet 实际花费超过 30 美金，远超预期。

## Henry 的原话

> 我今天实际花了30多美金的token，和你计算的不符

## 根因分析（当天查到的）

- **GBrain subagent 今日合计**：204 次 LLM call，input ~6M tokens，output ~207k tokens，按 Sonnet $3/M + $15/M 粗算约 **$21+**
- 主要大头：Dream Cycle 修复后，synthesize backlog 批量处理了大量 transcript（job 490 单次约 $2.91）
- Signal Capture 每 30 分钟用 Sonnet，叠加贡献 ~$1.94
- OpenClaw / gpt-5.5 侧 ~$4.48
- 调试期间多次手动重跑 + 失败重试放大了成本

## Henry 做出的决策

1. **Dream Cycle 从每天两次（02:00 + 13:00）改为只在凌晨 02:00 执行一次**
2. **修完 Synthesis 问题后，不直接启动 Dream Cycle 测试；先报告问题和修法，讨论没有风险后再执行**
3. 建议降成本：Signal Capture 从 Sonnet 改 Haiku，或改成"先轻量分类，只有信号再 Sonnet"

## 背景

当天主要任务是修 GBrain Dream Cycle 的 synthesize 失败问题（Unicode/NUL 字符导致 LiteLLM 炸、put_page 空参数循环）。修完后 Dream Cycle 首次真实处理了 backlog，产生了超预期的 token 消耗。

[Source: User, Telegram, 2026-05-15]
