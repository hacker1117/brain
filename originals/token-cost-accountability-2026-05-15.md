---
type: original
title: Token 费用 $30+ 事件：成本意识与核查习惯
date: '2026-05-15T00:00:00.000Z'
tags:
  - accountability
  - ai-ops
  - cost-control
  - gbrain
  - token-spend
---

# Token 费用 $30+ 事件：成本意识与核查习惯

2026-05-15，Henry 发现当日实际 token 花费远超预期：

> "我今天实际花了30多美金的token，和你计算的不符"

助手最初估算为 ~$5.9（只看了 cron finished 记录），但真实账单约 $30+。

**根因分析（事后）：**
- GBrain subagent 批量 synthesize 处理了积压的 transcript backlog（25 个 transcript）
- 多次调试失败后重试，每次重试都消耗 Sonnet 大上下文
- 单个最贵 job（490）约 $2.91，共 204 次 LLM call
- Signal Capture 每 30 分钟跑一次 Sonnet，累积成本不可忽视

**Henry 的行动：**
- 要求彻底核查费用来源，不接受"局部查看"的估算
- 提出降成本方向：Signal Capture 改 Haiku，或先轻量分类再决定是否 Sonnet 写入
- 后续推动 Dream Cycle 加成本保护 / 限制每轮处理 transcript 数量

[Source: User, Telegram, 2026-05-15]
