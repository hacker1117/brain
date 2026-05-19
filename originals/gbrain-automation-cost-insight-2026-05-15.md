---
type: original
title: GBrain 自动化链路成本洞察 — 实际花费30美金
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - signal-capture
---

# GBrain 自动化链路成本洞察

## 事件

2026-05-15，Henry 反馈今天实际 token 费用约 **30 多美金**，与 AI 估算的 $5.9 严重不符。

## Henry 原话

> "我今天实际花了30多美金的token，和你计算的不符"

## 根因分析（事后确认）

- **主要大头**：GBrain subagent 今日共 204 次 LLM 调用，input 约 6,052,856 tokens，按 Sonnet 价格约 $21.27
- **估算遗漏**：最初只看了 cron finished 记录，没有算 GBrain Minions subagent audit
- **最贵单个 job**：job `490`，input 679,681 + output 58,153，约 $2.91，因 `brain_put_page` 序列化失败导致输出异常放大
- **Dream Cycle backlog 批处理**：修完 Unicode/NUL 后，synthesize 终于能跑，一次性处理了大量历史 transcript
- **Signal Capture**：每 30 分钟一轮 Sonnet，累积也不小

## 优化方向（已识别）

1. Signal Capture 改用 Haiku 或轻量分类 → 只有有信号时才升 Sonnet
2. Dream Cycle 加成本保护 / 每轮限制 transcript 处理数量
3. subagent `put_page` 空参数循环已修复（max_turns: 30→8，timeout: 30min→12min）

## 关联

- [[preferences/dreamcycle-execution-policy]]
- [[people/henry-lee]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-15]
