---
type: original
title: GBrain Dream Cycle 基础设施修复（2026-05-14）
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - dream-cycle
  - fix
  - gbrain
  - infrastructure
---

# GBrain Dream Cycle 基础设施修复（2026-05-14）

今天做了一批 GBrain 基础设施改动，把 Dream Cycle 的可靠性从"基本跑不起来"提升到"能自动跑"。

## Session Exporter 建立

建立了 `/Users/lihengyi/.gbrain/scripts/export-openclaw-sessions.py`，定期把 OpenClaw JSONL session 转成 `.txt` 放到 `~/.gbrain/sessions/`，供 Signal Capture 和 Dream Cycle 读取。

**原来的问题**：Dream Cycle 和 Signal Capture 都需要 transcript，但 OpenClaw session 数据是 JSONL 格式，没有现成脚本转出来。

## Dream Worker 环境变量缺失

**问题**：常驻 `gbrain jobs work` worker 没有继承 `ANTHROPIC_*` 环境变量，导致 synthesize 子任务被 worker 抢走后失败。patterns / synthesize 等需要 LLM 的 phases 在自动运行时无法执行。

**修复**：让 LaunchAgent 启动脚本正确注入 LiteLLM/Anthropic 路由环境变量。

## Dream synthesize cooldown bug

**问题**：GBrain 代码里 `parseInt(value) || 12`，当 `cooldown_hours=0` 时被当成 falsy，实际 fallback 成 12 小时。导致临时测试时无法绕过 cooldown。

**临时处理**：设 `cooldown_hours=-1` 让 `Math.max(0, -1)` 变成真 0。

## Worker 并发和 Timeout 调整

- timeout 从默认值改成 90 分钟（synthesize 长任务需要）
- worker concurrency 调到 2（如果 GBrain queue 支持）

## 整体 Dream Pipeline 流程确认

```
export-openclaw-sessions.py（每 45 分钟）
→ Signal Capture cron（读 sessions/*.txt，提取 signals → GBrain pages）
→ Dream Cycle（夜间，读同 sessions corpus，patterns + synthesize）
```

关键：**Signal Capture 是快速提取路径，Dream 是深度 synthesis 路径，两者读同一个 transcript corpus，不互相阻塞。**

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
