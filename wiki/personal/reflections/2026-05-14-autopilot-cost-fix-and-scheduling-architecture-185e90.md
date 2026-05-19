---
type: reflection
title: 2026 05 14 Autopilot Cost Fix And Scheduling Architecture 185e90
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - bug-fix
  - cost-control
  - dream-cycle
  - gbrain
  - patterns
  - reflection
  - scheduling
---

# GBrain Autopilot 成本修复：patterns Bug 根治 + 轻重任务拆分的最终架构

**Session:** 2026-05-14 13:00–15:09 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `185e90`)

## 背景

在发现 Sonnet 账单 30 多美元并定位到 `patterns` phase 为主要元凶后（见 [[wiki/personal/reflections/2026-05-14-gbrain-autopilot-cost-explosion-2338b7]]），今天完成了实际修复和架构重组。

## patterns phase 的 Bug：缺少幂等保护

`synthesize` phase 有保护机制：

- `dream.synthesize.last_completion_ts`
- 12h cooldown
- transcript content hash（同一文件不重复处理）

但 `patterns` phase **完全没有**类似保护：

- 每次 autopilot-cycle 都收集最近 30 天 reflections
- 只要 reflections 数量 >= 3，就提交一个 Sonnet subagent
- 没有 cooldown，没有 reflection-set hash，没有「这组 reflections 已分析过」的标记
- 即使最后判断「没有新 pattern 要写」，也已经花掉 10 万+ input tokens

这符合 Henry 的「Bug 假设」：

> 「我依然有一个假设，就是 GPT brain 的设计方不会设计这么浪费 token 的方式。那么我们再做一个检查，就是说 autopilot 的 sub agent 消耗了这么多的 token。是不是因为某些 bug 的存在？就比如说……这个 subagent 应该已经处理完了某一些内容应该标记上，然后就不再处理了，但它还在反复地处理。」

Henry 的直觉被证实正确：这不是「昂贵的默认设计」，而是一个缺少幂等保护的漏洞。

## 修复方案（两步）

### 第一步：止血 — 12h cooldown（Henry 决策）

Henry 的判断：

> 「好的，你先做最小的改动，加上一个 12 小时的 cool down 吧。然后你可以测试一下，没问题了的话，就再补 hash。」

给 `patterns` 加 cooldown：

- 新配置：`dream.patterns.cooldown_hours`（默认 12）
- 状态键：`dream.patterns.last_completion_ts`
- 在 Supabase GBrain 配置里设置当前时间，立即止血

验证：autopilot-cycle `#422` 确认 `patterns = skipped: cooldown_active`

### 第二步：根治 — reflection-set hash

加 hash 的理由：patterns 的本质不是「时间到了就跑」，而是「reflections 集合变了才值得跑」：

- 新状态键：`dream.patterns.last_reflection_set_hash`
- hash 计算：`hash(reflection slug + updated_at + title/content excerpt)`
- 判断逻辑：hash 未变 → 直接 skip；hash 变了且 cooldown 过期 → 真正跑
- 跑完后更新 hash + timestamp

**commit:** `9518968 Throttle recurring pattern detection`  
测试：`bun test test/e2e/dream-patterns-pglite.test.ts` ✅ 9 pass

## 最终调度架构（Henry 决策）

最终架构由 Henry 明确拍板：

> 「我觉得就按照你的建议改策略吧，Autopilot 只做轻量的，然后 Cron 来做深度的任务，但是这个深度的任务可以每天中午 1 点执行一次和每天晚上 2 点执行一次。」

### Autopilot（高频轻量维护）

只跑 light phases：

```
lint → backlinks → sync → extract → extract_facts
→ recompute_emotional_weight → embed → orphans → purge
```

不再跑：`synthesize / patterns / consolidate`

### Deep Dream Cron（每天 02:00 + 13:00，Asia/Shanghai）

```
cron: 0 2,13 * * *
```

跑完整 Dream cycle，包含所有重任务。

**Cooldown 调整：** `synthesize.cooldown_hours = 10`，`patterns.cooldown_hours = 10`
→ 保证 02:00 → 13:00（11h）和 13:00 → 次日 02:00（13h）都能跑

**commit:** `b2d9186 Run autopilot as light maintenance only`

## 验证结果

修复后 autopilot cycles `#422` 到 `#440`：

- `synthesize` = `skipped: cooldown_active`
- `patterns` = `skipped: cooldown_active` / `already_processed`
- 没有新 subagent 启动
- 每轮耗时从 90–200s+ 降至 31–34s

## 认识论注记

Henry 在这个排查过程中展示了一个关键的「反向推理」模式：

> 「G-Brain 的创造者 Gary Tan 他自己是接触着比我更多的信息，管理着比我更多的项目，他也没有用这么超额的这个 token 费用。」

用「设计者自己不会接受这个结果」来反推「这里一定有 Bug」——这是一种以实践推断设计意图的调试哲学。而不是接受「默认行为就是如此」。

---
*Related: [[wiki/personal/reflections/2026-05-14-gbrain-autopilot-cost-explosion-2338b7]] · [[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]] · [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]*
