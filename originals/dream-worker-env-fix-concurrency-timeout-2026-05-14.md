---
type: original
title: Dream Worker 修复：环境变量继承、并发数 2、Timeout 90min
date: '2026-05-14T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - worker
---

# Dream Worker 修复：环境变量继承、并发数 2、Timeout 90min

## 背景

首次真实跑通 Dream Cycle 后（见 [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]），发现常驻 `gbrain jobs work` worker 存在三个结构性问题，Henry 明确决策修复。

## Henry's exact words

> 「好的，就按照你的思路改吧。首先让常驻的Worker继承对应的环境变量，去访问litellm。然后把timeout的时间可以改成90分钟，同时也把Worker的concurrency调到两个，如果支持的话。」

## 三个问题及决策

### 1. Worker 缺 ANTHROPIC_* 环境变量

常驻 `gbrain jobs work` worker 有 Supabase DB env，但没有 `ANTHROPIC_*` env，导致它抢到 synthesize/patterns 子任务时会失败（LLM 调用报错）。

**决策**：让 worker 重新继承 GBrain autopilot 脚本里的 LiteLLM/Anthropic 路由环境变量（通过 LaunchAgent plist 或启动脚本注入）。

### 2. Timeout 过短

默认 job timeout 导致长时间 synthesize 任务可能被 kill。

**决策**：将 timeout 改为 90 分钟。

### 3. Worker 并发数为 1

单个 worker 串行跑多个子任务效率低，遇到独立任务时无法并行。

**决策**：将并发数调整为 2（如 GBrain 支持）。

## 影响范围

这三个修复是 Dream Cycle 夜间自动运行可靠性的基础。如果 worker 没有正确的 LLM env，synthesize/patterns 永远会在夜间失败，即使触发了 Dream 也不会产生任何有意义的合成输出。

## 关联

- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[originals/build-openclaw-session-transcript-exporter-for-gbrain]]
- [[dream-cycle-summaries/2026-05-14]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
