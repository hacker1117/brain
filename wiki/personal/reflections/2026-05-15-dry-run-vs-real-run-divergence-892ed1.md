---
type: concept
title: 2026 05 15 Dry Run Vs Real Run Divergence 892ed1
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - dry-run
  - gbrain
  - postgres
  - testing
  - unicode
---

# Dry-run 通过 ≠ 真实跑通过 — synthesize 修复过程中发现的测试盲区

在 2026-05-15 的 Dream Cycle synthesize 修复过程中，出现了一个典型的测试陷阱：

**Dry-run 连续通过，但真实执行连续失败。**

## 具体情况

修复第一层（surrogate emoji）后，dry-run 已经显示 `synthesize: ok`，34 个 transcript 中 26 个进入合成。但真实跑仍然失败，错误从 `surrogates not allowed` 变成了 `unsupported Unicode escape sequence`。

assistant 当时的诊断：

> 「有问题。上一轮真实跑还是失败了，但错误已经从 `surrogates not allowed` 变成了新的 `unsupported Unicode escape sequence`。说明 emoji/surrogate 那层修掉了，下一层是 transcript 里还有某种非法 `\u...` 转义文本。」

根因：**dry-run 不写 Postgres job**，所以不会触发 `jsonb` 对 `\u0000` NUL 字符的校验。真实写入路径有一个 dry-run 路径没有的 Postgres 写 step。

具体脏点：一个 OpenClaw/GWC transcript 里有 `GPT\u00004o`（字面文本），导出后变成真实 NUL 字符，Postgres jsonb 拒绝写入。

## 为什么这个模式重要

这是一个经典的「测试覆盖差距」案例：

- Dry-run 测的是「能不能进入 synthesize」
- 真实跑测的是「整个写入路径是否无误」（包括 Postgres schema 限制）
- 两条路径在「哪些字符合法」上有不同的约束

修复不能只清洗到模型接受为止，必须同时满足：
1. LiteLLM/Python 侧的字符合法性（surrogate）
2. Postgres jsonb 侧的字符合法性（NUL/\u0000）
3. GBrain tool schema 侧的参数合法性（put_page 空参数）

## 三层修复

最终采用三层防护，见 [[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]]：

- 数据清理层（export 脚本 + ingest pipeline + transcript-discovery 入口）
- 工具校验层（put_page 强校验）
- 资源保护层（max_turns、chunk timeout 收紧）

## 决策

修复完成后，Henry 明确：先不启动真实 Dream Cycle，报告问题和修复方案，讨论确认无风险后再执行，见 [[wiki/personal/reflections/2026-05-15-debug-report-first-then-execute-892ed1]]。

## 相关

- [[originals/gbrain-automation-cost-discovery-2026-05-15]] — 本次调试的成本背景
- [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]] — 前一天的 synthesize 环境修复
