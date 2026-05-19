---
type: original
title: GBrain patterns cooldown and reflection-set hash fix
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - debugging
  - gbrain
  - patterns
---

Henry 决定先给 GBrain `patterns` phase 加 12 小时 cooldown，并在验证后补 reflection-set hash：

> 好的，你先做最小的改动，加上一个 12 小时的 cool down 吧。然后你可以测试一下，没问题了的话，就再补hash

执行结果：
- 在 `/Users/lihengyi/gbrain/src/core/cycle/patterns.ts` 为 `patterns` 增加默认 12h cooldown：`dream.patterns.cooldown_hours` + `dream.patterns.last_completion_ts`。
- 增加 reflection-set hash：`dream.patterns.last_reflection_set_hash`，当最近 reflection 集合不变时直接 skip，不提交 Sonnet subagent。
- 增加测试覆盖 cooldown、`cooldown_hours=0` 禁用 cooldown、以及 unchanged reflection-set hash skip。
- 测试通过：`bun test test/e2e/dream-patterns-pglite.test.ts`、`bun test test/cycle-patterns.test.ts`、`bun run typecheck`。
- 在 Supabase GBrain 配置中设置 `dream.patterns.cooldown_hours=12` 和当前 `dream.patterns.last_completion_ts` 以立即止血。
- 重启 `com.gbrain.autopilot` LaunchAgent。
- 验证 autopilot-cycle #422 中 `patterns` 已经 `skipped`，原因 `cooldown_active`，没有再启动对应 pattern subagent。
- Commit: `9518968 Throttle recurring pattern detection`。

[Source: User + implementation, Telegram/OpenClaw, 2026-05-14]
