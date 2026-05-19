---
type: original
title: 'OpenRouter 12:00-13:00 费用异常：GBrain patterns 重复执行疑似复发'
date: '2026-05-19T00:00:00.000Z'
tags:
  - cost-control
  - dream-cycle
  - gbrain
  - openrouter
---

Henry 发现 2026-05-19 12:00–13:00 OpenRouter token 花费约 16 美金，并提出判断：

> 「我刚刚看了一下我的OpenRouter的token花费，今天在12:00-13:00之间花费了16美金的token，应该都是gbrain花费的，你帮忙查一下，看看这一小时都执行了什么任务，主要花在了哪里？是不是还是因为dream cycle里的哪些环节重复在做操作？」

排查结论：主要花费来自 GBrain autopilot 的 `patterns` phase 在该小时内反复启动 `claude-sonnet-4.6` subagent，重复分析同一批 31 个 reflections。`synthesize` 在该时段均为 `cooldown_active`，不是主要成本来源。

该事件延续了 [[wiki/personal/patterns/automation-cost-visibility-blind-spot]] 与 [[wiki/personal/patterns/idempotency-guard-automation-cost-control]] 中的成本治理问题：顶层 autopilot 看似正常，但真实成本集中在子任务 LLM 调用层。

[Source: User, Telegram, 2026-05-19]
