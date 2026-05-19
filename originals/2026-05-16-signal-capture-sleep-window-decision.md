---
type: original
title: Signal Capture 睡眠窗口暂停决策
date: '2026-05-16T00:00:00.000Z'
tags:
  - agent-ops
  - cron
  - decision
  - infrastructure
---

# Signal Capture 睡眠窗口暂停决策

## Henry 原话

「我接下来想让你做一个修改，就是这个 signal capture 每半小时执行一次的这个。每天的凌晨的1点到早上9点期间就不用做系统评估了，因为这段时间内基本上就是我的睡眠时间，你跑这个任务，也不会有什么实际的收获。」

## 决策内容

- Signal Capture cron 从「每30分钟无条件执行」改为「睡眠窗口（01:00–08:59 Asia/Shanghai）期间暂停」。
- 新 cron 表达式：`*/30 0,9-23 * * *`（含义：00:00、00:30 及 09:00–23:30 每半小时执行）
- Live Sync、Dream Cycle、Weekly Doctor 等后台维护任务不受影响。

## 背景逻辑

凌晨1点到早上9点是 Henry 的睡眠时间，期间不会有新的对话或输入，Signal Capture 跑空会造成资源浪费、无实际收益。最小化改动原则：只停 Signal Capture，不动其他任务。

## Related

- [[concepts/agent-ops]] 
- [[projects/ai-training-business]]

[Source: User, Telegram, 2026-05-16]
