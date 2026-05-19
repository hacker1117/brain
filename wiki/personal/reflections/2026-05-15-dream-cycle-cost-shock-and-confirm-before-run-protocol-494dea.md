---
type: reflection
title: 2026 05 15 Dream Cycle Cost Shock And Confirm Before Run Protocol 494dea
date: '2026-05-15T00:00:00.000Z'
source: openclaw-session
session_id: f1697da1-46ec-4cb0-ab22-90d067dc80e6
transcript_hash: 494dea
tags:
  - automation
  - cost-awareness
  - decision-making
  - dream-cycle
  - gbrain
  - self-knowledge
---

# Dream Cycle 成本冲击与"先确认再执行"协议

**2026-05-15 — Telegram OpenClaw 会话**

## 成本冲击事件

今天 GBrain Dream Cycle synthesize 失败修复过程中，agent 先估算今日 token 支出约 **$5.9**，Henry 当场纠正：

> 「我今天实际花了30多美金的token，和你计算的不符」

实际支出约 $30+。根因：agent 只看了 cron finished 记录，遗漏了 GBrain minion subagents 的调用（204 次 LLM call，6M+ input tokens）。真正烧钱的是修复后 Dream Cycle 一次性处理积压 transcript 的 synthesize 批处理，不在 cron 视图里。

### 真实成本分布（事后还原）

| 来源 | 估算成本 |
|------|---------|
| GBrain patterns subagent（job 475/479/502）| ~$3.46 |
| Signal Capture Sonnet（每 30 分钟）| ~$1.94 |
| Deep Dream 报告 agent（调试多次重跑）| ~$0.52 |
| GBrain synthesize backlog 批处理（修复后一次性放量）| ~$21.27 |
| OpenClaw / gpt-5.5 侧 | ~$4.48 |

**教训**：自动化系统的成本必须在 job queue 层面审计，只看 cron 会遗漏 subagent 消耗；积压修复后的"一次性放量"是隐性成本峰值。

## "先确认再执行"协议

Henry 在调试中间做出了明确的协议要求：

> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

这是他面对"修复成本不透明的自动化系统"时的标准操作：**先修代码，先静态验证，先汇报，再人工确认，最后才执行**。不允许自动触发下一轮实跑，哪怕"看起来修好了"。

这个协议反映了 [[wiki/personal/patterns/foundations-first-sequencing]] 在成本控制场景下的应用：不在未验证的基础上继续投入资源。

## Dream Cycle 频次决策

同一次对话中，Henry 还做了另一个明确决策：

> 「整体的 Dreamcycle 我也不希望一天执行两次了，我希望只在晚上 2 点执行一次。」

原来 Dream Cycle 每天 13:00 和 02:00 各跑一次。这次调试显示 13:00 那次从未产出（delivery 配置为 none 且持续失败），是隐性重复成本源。改成只跑一次是直接的成本收敛动作。

## Signal Capture 的持续成本问题

Agent 指出 Signal Capture 每 30 分钟调用 Sonnet，是"持续成本源"，建议降级到 Haiku 或做轻量预分类。这个问题今天没有解决，是后续待处理项。参见 [[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]] 中的修复记录。

---

*Source: Telegram session 2026-05-15, OpenClaw transcript 494dea*
