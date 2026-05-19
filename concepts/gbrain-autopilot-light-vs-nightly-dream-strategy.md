---
type: concept
title: GBrain Autopilot 轻量维护 vs Nightly Dream 深度合成的调度策略
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - autopilot
  - cost-control
  - dream
  - gbrain
  - scheduling
---

# GBrain Autopilot 轻量维护 vs Nightly Dream 深度合成

## 核心原则

**不要让 autopilot 每 5 分钟跑完整 Dream cycle。**

Autopilot 适合做轻量自维护；Dream synthesis 应该每天一次或手动触发。

> "我也想让它只做必要的，不太花费 token 的工作。然后类似 Dream、Synthesize Patterns 之类的，都应该每天执行一次。"
> — Henry, 2026-05-14

## 职责分配

### Autopilot（高频，轻量，低 token）

保留阶段：
- `lint`：格式检查，无 LLM
- `backlinks`：补 backlink，无 LLM
- `sync`：`~/brain` → Supabase，无 LLM
- `extract`：links / timeline 规则提取，基本无 LLM
- `recompute_emotional_weight`：重算权重，无大模型
- `embed`：embedding 生成（有 token，但成本低，必要）
- `orphans`：孤儿页检查，无 LLM
- `purge`：清理软删除，无 LLM

### Nightly Dream Cycle（每天 02:00 Asia/Shanghai，完整）

包含阶段：
- 全部轻量阶段 +
- `synthesize`：处理 transcript，可能派 Sonnet subagent
- `patterns`：跨 session pattern detection，Sonnet
- `extract_facts`：对页面做事实抽取
- `consolidate`：facts 聚合成 takes

## 背景：GBrain 官方设计的问题

GBrain 官方设计中，`autopilot` 默认与 `gbrain dream` 共用完整 phase set（`jobs.ts` 注释："Shares the exact same phase set and ordering as `gbrain dream`"）。

这对常驻高频运行不合理：`patterns` phase 缺少幂等保护，每次只要 reflections >= 3 就无条件提交 Sonnet subagent，导致 5 分钟频率下费用爆炸（单日 $33+）。

Henry 的判断：
> "G-Brain 的设计方不会设计这么浪费 token 的方式，更倾向于认为它是一个 bug 造成的。"

确认属实：`patterns` 缺失幂等保护是设计缺陷，而非刻意设计。

## 当前实施状态（2026-05-14）

- `patterns` 已加 12h cooldown + reflection set hash 去重
- `synthesize` 原有 12h cooldown 保留
- 修复后 autopilot 高频运行不再触发 Sonnet subagent
- 调度策略"完全分离"尚未实施（autopilot 仍是 full phase set，只靠 cooldown 限流）

## 下一步（未实施）

真正的正确架构：
- `autopilot` 传 `phases` 参数，只执行 light set
- `synthesize/patterns/consolidate/extract_facts` 移出 autopilot，只在 cron 里触发

## 关联页面

- [[originals/gbrain-autopilot-patterns-cost-explosion-2026-05-14]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
