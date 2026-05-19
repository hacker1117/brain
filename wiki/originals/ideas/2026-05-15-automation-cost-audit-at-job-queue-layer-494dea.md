---
type: original
title: 2026 05 15 Automation Cost Audit At Job Queue Layer 494dea
date: '2026-05-15T00:00:00.000Z'
source: openclaw-session
session_id: f1697da1-46ec-4cb0-ab22-90d067dc80e6
transcript_hash: 494dea
tags:
  - architecture
  - automation
  - cost-awareness
  - gbrain
  - llm-ops
  - subagent
---

# 自动化系统的成本审计必须在 Job Queue 层，不能只看 Cron

## 核心洞察

2026-05-15 GBrain Dream Cycle 调试中发现：**cron 视角的成本估算严重低估真实支出**。

当 agent 只看 cron finished 记录时，估算今日 Sonnet 支出约 $5.9。实际账单约 $30+。

**差异来源**：GBrain 的 cron job 完成后会派出 subagent（minion jobs），这些 subagent 的 LLM 调用完全在 cron 视图之外。今天 synthesize backlog 批处理产生了 204 次 LLM call、6M+ input tokens，全部来自 subagent，而不是 cron 直接调用。

## 模型

```
错误的成本模型：
  cron_runs × avg_tokens_per_run = 总成本

正确的成本模型：
  cron_runs → spawn subagents → each subagent has N turns × M tools
                               └→ LLM calls not visible in cron log
  总成本 = Σ(subagent LLM calls) + cron orchestration
```

## 隐性成本放大器

1. **积压释放**：一个 bug 修好后，系统一次性处理所有积压任务，产生成本峰值
2. **失败重试**：每次 synthesize fail → 错误回喂模型 → 继续尝试 → job 490 消耗 679k input tokens
3. **高频轻任务累计**：Signal Capture 每 30 分钟 Sonnet，单次看起来小，日计约 $1.94
4. **max_turns 过大**：subagent max_turns=30 允许极长对话，空参数 bug 可以把 token 消耗放大几十倍

## 防护措施

- **成本审计入口**：必须从 LiteLLM/model gateway 的调用日志，而非 cron history
- **subagent max_turns 收紧**：30 → 8，快速失败胜过无限重试
- **积压处理限速**：每轮 Dream Cycle 限制处理 transcript 数量，避免修复后一次性放量
- **preflight dry-run**：启动任何 backlog 处理前先统计规模，确认后再执行

这与 Henry 的 [[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]] 中的"先确认再执行"协议直接对应。也呼应了 [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]] 中关于 Dream Cycle 基础设施可靠性的系列修复。

## 推论

> **当自动化链路里存在 subagent / child job 的时候，成本透明度需要跨层监控。单层视角（cron、session、LLM call）各自都只能看到一部分真相。**

---

*Source: OpenClaw Telegram session 2026-05-15, transcript 494dea*
