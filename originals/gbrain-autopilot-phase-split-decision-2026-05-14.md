---
type: original
title: GBrain Autopilot 轻重分层决策
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - autopilot
  - cost-control
  - dream-cycle
  - gbrain
---

# GBrain Autopilot 轻重分层决策

Henry 的明确决策：

> "我也想让它只做必要的，不太花费 token 的工作。然后类似 Dream、Synthesize Patterns 之类的，都应该每天执行一次。"

## 分层方案

### Autopilot 高频保留（轻量，低 token）

- `lint`：检查格式，无 LLM
- `backlinks`：补 backlink，无 LLM
- `sync`：brain → Supabase，无 LLM
- `extract`：links / timeline 规则提取，基本无 LLM
- `recompute_emotional_weight`：无大模型
- `embed`：有 token 但极低，必要
- `orphans`：孤儿页检查，无 LLM
- `purge`：清理软删除，无 LLM

### 从 Autopilot 移出（重量，走 Nightly Dream）

- `synthesize`：处理 transcript，可能派 Sonnet subagent
- `patterns`：跨 session pattern detection，Sonnet 消耗大
- `extract_facts`：可能走模型抽取
- `consolidate`：聚合 facts → takes，Sonnet synthesis

### Nightly Dream Cycle（每天 02:00 Asia/Shanghai）

每天一次全量，包含被剥离的重阶段。

## Autopilot 间隔调整

- 从默认 `300s`（5 分钟）调到 `900s`（15 分钟）或 `1800s`（30 分钟）
- 理由：已有 Live Sync cron 每 15 分钟，conversation ingest 每天 11:30/23:30

## 背景

今天排查发现：今日 Sonnet 消耗约 $30+ 的主因是 GBrain autopilot 以 5 分钟频率触发 Dream/pattern synthesis Sonnet subagent（共 66 次、353 次 LLM call、9.5M input token）。修复后预计从几十美元/天降到几美元/天。

## 关联

- [[originals/gbrain-dream-frequency-cost-control-2026-05-14]]
- [[originals/gbrain-autopilot-origin-vs-dream-cron-question-2026-05-14]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
