---
type: concept
title: Telegram Topics 与 GBrain Session 保留策略
date: '2026-05-14T00:00:00.000Z'
tags:
  - gbrain
  - session
  - telegram
  - topics
  - workflow
---

# Telegram Topics 与 GBrain Session 保留策略

Henry 在 2026-05-14 确认的操作方式。

[Source: User, Telegram, 2026-05-14]

## 核心结论

**可以在 Telegram 里删除 Topic，GBrain 仍然保留对话记录。**

原因：
- OpenClaw 已有定期 Signal Capture cron，会把 session transcript export 到 `~/.gbrain/sessions/`
- 一旦 transcript 已被 export，删除 Telegram topic 不影响 GBrain 里的对话记录

## 操作规范

1. 在 Telegram Group 里建一个新 Topic 讨论特定话题
2. 讨论过程中 OpenClaw 自动记录 session
3. 每 45 分钟 Signal Capture cron 会 export transcript 到 `~/.gbrain/sessions/`
4. 确认 export 完成后（或等待下一次 cron 执行后），可以安全删除 Telegram Topic
5. GBrain Dream Cycle 后续会从 session transcript 做深度 synthesis

## 注意

- 删 Topic 前最好等待一次 Signal Capture cron 执行
- 或手动运行 export 脚本确认已导出
- 导出后的 session 文件路径：`~/.gbrain/sessions/<date>-openclaw-topics-*`
