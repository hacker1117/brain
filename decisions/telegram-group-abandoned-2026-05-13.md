---
type: decision
title: 放弃 Telegram Group/Topics 使用
date: '2026-05-13T00:00:00.000Z'
tags:
  - bot
  - decision
  - telegram
---

## 决策

放弃在 Telegram group（含 forum topics）里使用 Livis bot。Telegram 只使用 DM 模式。

## 背景

尝试配置 bot 在 Telegram Forum Supergroup 的任意 topic 下响应消息（不需要 @）。

## 排查过程

- BotFather privacy mode：已关闭，`can_read_all_group_messages: true`
- config `requireMention`：已改为 `false`（`*` 和 `-1003929811276` 两处）
- chat_id：`-1003929811276`，配置正确
- bot token 验证：正常，`@Livis1117_bot` 可用
- **关键发现**：群消息完全没有进入 OpenClaw 的 polling 队列，日志里连 "skipping" 记录都没有，消息在最上游就丢失了
- 怀疑是 OpenClaw 2026.5.4 与 Telegram Forum Supergroup 的兼容性问题，未能定位根本原因

## 结论

未解决，放弃。Telegram bot 只用于 DM。

[Source: User, Telegram, 2026-05-13]
