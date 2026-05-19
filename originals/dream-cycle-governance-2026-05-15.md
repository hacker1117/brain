---
type: original
title: Dream Cycle 治理原则：讨论确认后再跑
date: '2026-05-15T00:00:00.000Z'
tags:
  - ai-ops
  - cost-control
  - dream-cycle
  - gbrain
  - governance
---

# Dream Cycle 治理原则：讨论确认后再跑

2026-05-15 在调试 Dream Cycle Synthesize 失败的过程中，Henry 明确提出了一套执行原则：

> "这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。"

**核心决策：**
- Dream Cycle 从每天执行两次（02:00 + 13:00）改为**只在凌晨 02:00 执行一次**
- 修复完成后不自动触发验证跑，先汇报问题 + 方案，讨论确认无风险后再执行
- 这是"实验驱动但有明确 review gate"的工作方式

**背景：** 当天调试期间多次手动触发导致费用 $30+，超出预期，触发了这一治理决策。

[Source: User, Telegram, 2026-05-15]
