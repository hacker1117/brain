---
type: original
title: GBrain 0.36.1 升级 — 本地 patch 取舍决策
date: '2026-05-19T00:00:00.000Z'
tags:
  - code-patch
  - decision
  - gbrain
  - technical-debt
  - upgrade
---

## 升级摘要

GBrain 从 `0.32.5` 升级到 `0.36.1.0`（2026-05-19）。

升级前：本地有 2 个 commit + 8 个未提交改动，与 upstream 分叉。

## Henry 的决策

> "之前关于autopilot、synthesize、dream的相关的改动也一起丢掉吧，保持和现在的状态一致。哪怕他现在有一点点烧token，我也能接受。我需要再观察一段时间。"

**核心原则**：不要把 workaround 永久化。先恢复到 upstream 行为，观察，再决定。

## 已丢弃的本地 patch

| 改动 | 原因 | 最终状态 |
|---|---|---|
| `skills/RESOLVER.md` managed skillpack block | 0.36.0 已废弃该机制（改为 scaffold/reference/harvest） | 丢弃 |
| `serve-http.ts` array items schema patch | 0.35.3.0 upstream 用 `paramDefToSchema` 更好地替代了 | 丢弃 |
| `extract_facts.entity_hints.items` patch | 0.35.3.0 upstream 已正式包含 | 丢弃 |
| `autopilot` light-only cycle 限制 | Henry 决定回到 upstream 默认，观察 | 丢弃 |
| `patterns/dream` 12h cooldown + reflection hash 去重 | 同上 | 丢弃 |
| `synthesize.ts` max_turns 8 / timeout 12min 限制 | 同上 | 丢弃 |
| `transcript-discovery.ts` surrogate/NUL sanitize | 无法确认是否仍有效，回滚观察 | 丢弃 |

## 保留的本地 patch

| 改动 | 原因 |
|---|---|
| `brain-allowlist.ts` JSON string input 自动 parse | 低风险 robustness，upstream 未覆盖 |
| `operations.ts` put_page/slug 校验 | 低风险 input validation |
| 对应测试文件 | 配套保留 |

验证：`33 pass / 0 fail`

## 升级后状态

- 版本：`0.36.1.0`
- DB schema：v73（migration 完成）
- brain_score: 80，embed_coverage: 100%
- orphan_pages: 135（较升级前下降，部分因 soft-delete 统计 bug 修复）

## 关键新能力（0.36.1.0）

- **Hindsight Calibration**：`gbrain calibration`，评估判断校准度
- **Dream Cycle 新 phases**：propose_takes / grade_takes / calibration_profile
- **ZeroEntropy embedding**：zembed-1 + zerank-2 可选
- **Temporal trajectory**：`gbrain__find_trajectory`，实体时间轴
- **orphans soft-delete 统计修复**（0.35.5.0）：orphan 数字更可信

[Source: User, Telegram/OpenClaw Transcript, 2026-05-19]
