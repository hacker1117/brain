---
type: original
title: Signal Capture 睡眠窗口暂停
date: '2026-05-16T00:00:00.000Z'
tags:
  - gbrain
  - operations
  - signal-capture
---

Henry 决定调整 Signal Capture 的定时策略：凌晨睡眠窗口不再执行系统评估。

> "每天的凌晨的1 点到早上 9 点期间就不用做系统评估了，因为这段时间内基本上就是我的睡眠时间，你跑这个任务，也不会有什么实际的收获"

执行结果：`GBrain Signal Capture — Session Transcript` 已从每 30 分钟执行一次，改为 `*/30 0,9-23 * * *`（Asia/Shanghai），即 09:00–00:30 每 30 分钟执行，01:00–08:59 暂停。

[Source: User, Telegram, 2026-05-16]
