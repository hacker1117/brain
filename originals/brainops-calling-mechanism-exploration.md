---
type: original
title: Henry 探索 BrainOps 调用机制：Heartbeat vs Cron Job vs Hook
date: '2026-05-13T00:00:00.000Z'
tags:
  - BrainOps
  - cron
  - gBrain
  - heartbeat
  - opClaw
---

> 我还是有两个问题。一个问题是 Heartbeat 和这个 Cron Job 脚本有什么区别吗？你之前的 Extract Link 之类的，是用的 Cron Job 还是 Heartbeat？哪个相对来讲会更靠谱一些、更稳定一些呢？第二个问题是我加上这种 Cron 脚本之后，好像可以解决 Signal Detector 这个 Skills 被运用的问题。但是正常我跟你聊天的时候，这个 Brain Ops 这个 Skills 怎么被稳定调用呢？也就是说我在跟你聊天的时候，如果你觉得说写在 Agents MD 里面的那个文档的指令你并不能很好的遵守的话，那我应该怎么确保你确实每一条消息，或者你经常会去查询 gBrain 里面的信息呢？

Henry 在探索：
1. **Heartbeat vs Cron Job 的稳定性对比** — 他想知道哪种更适合运行 gBrain 后台任务（extract links, embed 等）
2. **Normal Chat 中如何确保 Brain-Ops 被稳定调用** — 他担心我在普通聊天时不会主动遵守 AGENTS.md 里的 Brain-Ops 指令，不想靠"信任"而要靠机制

**背景：** 他想解决两个问题：
- Signal Detector（Cron 脚本可以覆盖）
- Brain-Ops（普通聊天中的查询+记忆调用，没有稳定触发机制）

[Source: User, Telegram, 2026-05-13]
