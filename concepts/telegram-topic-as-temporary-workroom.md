---
type: concept
title: Telegram Topic 作为临时工作间，结束后归档并删除
date: '2026-05-14T00:00:00.000Z'
tags:
  - gbrain
  - openclaw
  - telegram
  - topics
  - transcript
  - workflow
---

# Telegram Topic 作为临时工作间，结束后归档并删除

## 核心用法

Henry 可以在 Telegram group 里为一个特定话题临时创建 topic，用它作为一次讨论/任务推进的工作间。讨论结束后，topic 可以在 Telegram 里删除，以避免 group 里长期堆积太多 topics。[Source: User, Telegram topic Getseed, 2026-05-14]

## 保存机制

Telegram topic 删除不等于 OpenClaw / GBrain 本地 session 删除。只要消息已经被 bot 收到并进入 OpenClaw session，本地 JSONL transcript 会保留。GBrain exporter 后续从 OpenClaw 本地 session 文件导出到 `~/.gbrain/sessions/*.txt`，供 Signal Capture / Dream Cycle 后续处理。[Source: Tool validation + User discussion, Telegram topic Getseed, 2026-05-14]

当前验证样例：
- Telegram group: `telegram:-1003929811276`
- Topic ID: `40`
- Topic name: `Getseed`
- OpenClaw session key: `agent:main:telegram:group:-1003929811276:topic:40`
- OpenClaw transcript path: `~/.openclaw/agents/main/sessions/542a6767-a6b3-4fc7-908a-409cc4bd65b0.jsonl`

## 推荐操作流程

1. 为一个具体主题创建 Telegram topic。
2. 在 topic 内集中讨论任务、决策、测试结果和下一步。
3. 讨论结束时发一句：`这条 topic 结束，帮我归档`。
4. Agent 手动确认/触发 exporter，把 OpenClaw session 导出到 `~/.gbrain/sessions/`，必要时同步写入 GBrain page。
5. Henry 再删除 Telegram topic。

## 注意事项

- 普通 topic：讨论结束后等待 1-2 分钟再删除，确保最后几条消息落盘。
- 重要 topic：删除前先让 Agent 明确回复“已归档，可以删除”。
- 删除 Telegram topic 只影响 Telegram 界面整洁，不应影响本地 transcript 与 GBrain 后续处理。

[Source: User, Telegram topic Getseed, 2026-05-14]
