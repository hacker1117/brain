---
type: original
title: GBrain autopilot light and cron deep strategy
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - cron
  - dream-cycle
  - gbrain
---

Henry 决定采用新的 GBrain 调度策略：

> 我觉得就按照你的建议改策略吧，Autopilot 只做轻量的，然后 Cron 来做深度的任务，但是这个深度的任务可以每天中午 1 点执行一次和每天晚上 2 点执行一次。

执行结果：
- `autopilot` 改为 light maintenance only，提交 `b2d9186 Run autopilot as light maintenance only`。
- 高频 autopilot phases 现在只包含：`lint`, `backlinks`, `sync`, `extract`, `extract_facts`, `recompute_emotional_weight`, `embed`, `orphans`, `purge`。
- 高频 autopilot 明确排除：`synthesize`, `patterns`, `consolidate`。
- 重启 `com.gbrain.autopilot`。
- 验证 autopilot job #440 的 Data 中只包含 light phases。
- OpenClaw cron `GBrain Deep Dream Cycle` 改为每天 Asia/Shanghai `02:00` 和 `13:00` 执行，cron expr `0 2,13 * * *`。
- 为了让 02:00 与 13:00 都能执行深度任务，将 `dream.synthesize.cooldown_hours` 和 `dream.patterns.cooldown_hours` 调整为 `10`。这样 02→13 间隔 11 小时，13→次日02 间隔 13 小时，均可通过 cooldown。
- 将 `dream.patterns.last_completion_ts` 临时调早为 `2026-05-14T07:00:00Z`，确保下一个 02:00 deep cron 不会被之前为止血设置的 patterns cooldown 卡住。
- 明天 14:10 设置了一次审计提醒，检查 13:00 deep cycle 和 light autopilot 分离是否正常、token 是否受控。

[Source: User + implementation, Telegram/OpenClaw, 2026-05-14]
