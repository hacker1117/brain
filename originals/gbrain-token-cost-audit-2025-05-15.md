---
type: original
title: GBrain 自动化链路今日 Token 花费核查
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - signal-capture
---

## 事件

Henry 发现今天 OpenRouter 实际账单 **30 多美金**，与 AI 初次估算的约 $5.9 相差近 6 倍。经排查确认，主要成本来源是 GBrain 自动化任务链路，而非 Henry 日常对话。

## Henry 原话

> 我今天实际花了30多美金的token，和你计算的不符

## 根因拆解

**最大头：GBrain subagent batched synthesize（$21.27+）**
- Dream Cycle 调试期间修复 Unicode/NUL 后，backlog transcript 批量进入 synthesize
- 204 次 LLM call，input 6,052,856 tokens，output 207,453 tokens
- 单个最贵 job（490）：679k input + 58k output ≈ $2.91；还因 `put_page {}` 空参数导致循环重试放大消耗

**次要：Signal Capture 高频 Sonnet（约 $1.94）**
- 每 30 分钟调用 Sonnet 一次，全天持续
- 当前是持续成本源

**其他：Dream Cycle 报告 agent、主会话等（约 $0.52+）**

## 教训与结论

- 自动化 backlog 批处理一旦解锁，可以在短时间内产生远超日常水平的消耗
- Signal Capture Sonnet 模式需要降级（改 Haiku 或轻量分类优先）
- 需要为 Dream Cycle 设置 preflight / transcript count 阈值保护
- 初次估算只看 cron 记录，漏了 GBrain subagent audit，导致数字严重低估

[Source: User, Telegram, 2026-05-15]
