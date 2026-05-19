---
type: original
title: GBrain 自动化任务是 Token 花费大头
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - dream-cycle
  - gbrain
  - signal-capture
---

# GBrain 自动化任务是 Token 花费大头

今天实际花了 30 多美金的 token，和初步估算的 $5.9 不符。  
根本原因：**GBrain 自己派出去的 subagent 不在 cron finished 记录里**，所以只看 cron run history 会严重低估。

真实大头来源：

1. **Deep Dream Cycle 的 synthesize backlog 批处理**
   - jobs 480–501 集中处理大量 transcript
   - 单个最贵的 job 490：input 679,681 + output 58,153，约 $2.91
   - GBrain subagent 今日合计：input 6,052,856 + output 207,453，约 **$21.27**
   - 根因：修完 Unicode/NUL 后，Dream Cycle 终于开始真实处理 backlog

2. **Signal Capture 高频 Sonnet**
   - 约 $1.94，每 30 分钟用 Sonnet，持续成本源

3. **Deep Dream 报告 agent 重试**
   - 约 $0.52，今天调试次数多放大了

教训：**GBrain subagent 是独立的成本黑盒**，cron log 不透明；应有实时 token budget 上限和每轮 preflight 统计。

> 原话：「我今天实际花了30多美金的token，和你计算的不符」

[Source: User, Telegram, 2026-05-15]
