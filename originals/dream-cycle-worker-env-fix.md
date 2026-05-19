---
type: original
title: Dream Cycle Worker 环境变量修复记录
date: '2026-05-14T00:00:00.000Z'
tags:
  - anthropic
  - autopilot
  - bug-fix
  - dream-cycle
  - env
  - gbrain
  - litellm
  - worker
---

# Dream Cycle Worker 环境变量修复记录

## 背景

手动跑完整 Dream Cycle 时发现：常驻 `gbrain jobs work` worker 能拿到 Supabase DB env，但**没有 `ANTHROPIC_*` env**，所以它抢到 synthesize 子任务时会失败。

## 问题详情

1. **Worker 缺 ANTHROPIC env**：synthesize/patterns 任务需要调 LLM，但 worker 进程的环境变量没有 LiteLLM/Anthropic 路由配置，导致任务失败
2. **Timeout 过短**：synthesize 任务耗时长，需要从默认值改为 90 分钟
3. **Worker 并发=1**：只有单 worker，耗时任务会阻塞其他任务

## 修复内容

1. 让常驻 worker 继承 LiteLLM/Anthropic 路由环境变量
2. Timeout 改成 90 分钟
3. Worker 并发调到 2（如果 GBrain 支持）

## 影响

- 修复前：夜间自动 Dream 的 synthesize/patterns 阶段会失败（worker 没有 LLM 调用能力）
- 修复后：完整 Dream pipeline 可以自动化运行

## 另一个 cooldown bug

`cooldown_hours=0` 会被代码里的 `parseInt(value) || 12` 当成 falsy，实际回落成 12 小时。临时 workaround：设为 `-1`，代码 `Math.max(0, -1)` 会变成真正的 0。

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
