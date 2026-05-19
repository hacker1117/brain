---
type: original
title: Dream Cycle 治理决定：每天仅凌晨 2 点执行，修复后先报告再测试
date: '2026-05-15T00:00:00.000Z'
tags:
  - cron
  - decision
  - dream-cycle
  - gbrain
  - governance
---

## Henry 的决定（原话）

> 「整体的 Dreamcycle 我也不希望一天执行两次了，我希望只在晚上 2 点执行一次。」
>
> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

— Henry, 2026-05-15 19:10 (GMT+8)

## 决定内容

1. **Dream Cycle 频率**：从每天 02:00 + 13:00 两次 → 仅保留凌晨 **02:00 Asia/Shanghai** 一次
2. **修复工作流**：任何 Dream Cycle 底层修复完成后，先向 Henry 汇报问题和方案，讨论确认无风险后，才能执行下一轮测试
3. **不自行触发测试**：修复期间不得擅自触发 Dream Cycle 实跑

## 背景

当天 synthesize 模块因 Unicode/NUL + `put_page {}` 空参数循环问题导致多次调试失败，并因此产生意外高额 token 消耗（30+ 美金）。

[Source: User, Telegram, 2026-05-15]
