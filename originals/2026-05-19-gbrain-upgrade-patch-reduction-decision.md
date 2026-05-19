---
type: original
title: GBrain 升级后减少本地 patch 的决策
date: '2026-05-19T00:00:00.000Z'
tags:
  - decision
  - gbrain
  - local-patches
  - upgrade
---

Henry 决定：

> 你建议的所有要丢掉的内容，就直接丢掉吧。
> 之前关于autopilot、synthesize、dream的相关的改动也一起丢掉吧，保持和现在的状态一致。哪怕他现在有一点点烧token，我也能接受。我需要再观察一段时间。
> 其他的相关的决策都按照你的来。

执行含义：升级到 GBrain 0.36.1.0 后，优先保持 upstream 当前行为，丢弃旧 managed skillpack block、autopilot light-cycle 本地策略、patterns cooldown、synthesize timeout/turn cap、transcript sanitize 等历史 workaround；仅保留低风险 MCP/subagent 输入兼容和 put_page 参数校验补丁。

[Source: User, Telegram, 2026-05-19]
