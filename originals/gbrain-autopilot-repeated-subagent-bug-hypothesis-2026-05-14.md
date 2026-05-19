---
type: original
title: GBrain autopilot repeated subagent bug hypothesis
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - debugging
  - gbrain
---

Henry 对 GBrain autopilot 的高 token 消耗提出了 bug 假设：

> 我依然有一个假设，就是 GPT brain 的设计方不会设计这么浪费 token 的方式。那么我们再做一个检查，就是说 autopilot 的 sub agent 消耗了这么多的 token。是不是因为某些 bug 的存在？就比如说它认为某一个任务一直没执行完，所以反复地运行。或者说是，这个 subagent 应该已经处理完了某一些内容应该标记上，然后就不再处理了，但它还在反复地处理。我觉得我更倾向于认为它是一个 bug 造成的。如果 autopilot 它本身就是这个 brain 自带的，并且它还推荐。默认的这个间隔是 300 秒的话。

[Source: User, Telegram, 2026-05-14]
