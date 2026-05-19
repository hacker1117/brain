---
type: concept
title: Dream Cycle 首次真实跑通：锁冲突、Cooldown Bug 与 Supabase Env 三重障碍
date: '2026-05-14T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - openclaw
---

# Dream Cycle 首次真实跑通：锁冲突、Cooldown Bug 与 Supabase Env 三重障碍

## 背景

Henry 在这次 settings 会话中明确授权跑一次完整的真实 Dream 流程测试：

> 「你现在来手动跑一遍完整的Dream流程吧。我需要实际的测试一下，我可以接受里面的成本。」

这是 [[dream-cycle-summaries/2026-05-14]] 对应的配置和调试过程。

---

## 遭遇的三重障碍

### 1. `cycle_already_running` 锁冲突

手动触发时，autopilot-cycle `259` 正在自动运行，导致锁冲突。解决方式：等待 cycle 259 结束后再手动触发。

> 「当前还有一个自动触发的 autopilot-cycle `259` 正在跑；我等它结束后再手动跑，避免锁冲突。」

这个情况揭示了手动 Dream 触发与自动 autopilot 触发之间需要协调的问题。

### 2. `cooldown_hours=0` 被 `parseInt` 当成 falsy

为了绕过 12 小时 synthesize cooldown，将 `dream.synthesize.cooldown_hours` 临时设为 `0`，但代码里的实现是：

```js
parseInt(value) || 12
```

`parseInt("0")` 返回 `0`，而 `0 || 12` 等于 `12`，于是 cooldown 又回落成 12 小时。这是一个经典的 JS truthy/falsy 陷阱。

**解决方案**：将 cooldown 设为 `-1`，代码用 `Math.max(0, -1)` 将其截断为 `0`，绕过了 falsy 判断。

### 3. Supabase `DATABASE_URL` 未注入临时修改

第一次临时修改 `cooldown_hours` 时没有带 `GBRAIN_DATABASE_URL`，改到的是一个非 Supabase 配置，导致修改未生效。这和 LaunchAgent 重新载入的问题同源：

> 「我改了 GBrain autopilot 的环境变量脚本，得让 LaunchAgent 重新载入一次，否则正在跑的 autopilot 进程未必拿到新的 Anthropic/LiteLLM 路由。」

环境变量的多副本问题（本地 `.env` vs LaunchAgent plist vs 进程继承）是这个系统的一个持续风险点。

---

## 最终结果

完整 cycle 跑完：
- patterns 阶段用 Claude 成功写了 2 个 pattern page
- synthesize 阶段经三次尝试（cooldown 绕过）最终成功
- 6 个页面写入并 embedded

---

## 系统性教训

这次调试是 [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]] 的又一个实例：每次"首次真实测试"都会暴露多个互相叠加的层级问题，而不是单一 bug。见 [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]。

---

*Related: [[dream-cycle-summaries/2026-05-14]] · [[originals/henry-dream-signal-architecture-understanding-2026-05-14]] · [[originals/build-openclaw-session-transcript-exporter-for-gbrain]]*
