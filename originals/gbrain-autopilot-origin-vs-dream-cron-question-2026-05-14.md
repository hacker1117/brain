---
type: original
title: GBrain Autopilot 来源与 Dream Cron 区分问题
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - dream-cycle
  - gbrain
---

Henry 对 GBrain 自动维护来源的追问：

> “这个 autopilot 是这 brain 本身就有的，就推荐的吗？还是我们今天写进去的呢？”

判断方向：需要区分 GBrain 内置的 `autopilot` daemon（默认 interval 300s）与 OpenClaw 配置的 nightly `GBrain Dream Cycle` cron（每天 02:00）。成本控制问题不在于是否存在 autopilot，而在于不应让高频 autopilot 触发 Sonnet 级别的 deep synthesis/pattern subagent。

[Source: User, Telegram, 2026-05-14]
