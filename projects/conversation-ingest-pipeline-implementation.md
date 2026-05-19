---
type: project
title: Conversation Ingest Pipeline 实现记录
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - getnote
  - getseed
  - ingestion
---

# Conversation Ingest Pipeline 实现记录

## Latest Update — 2026-05-14 17:16

Henry 决定：Claude 分类兜底暂不实现，只记录为未来可考虑的拓展项。

已完成定时同步：
- 新增 `scripts/sync-recent.mjs`，作为统一 scheduled sync entrypoint。
- 新增 npm script：`npm run sync:recent`。
- 默认同步最近 3 天（可通过 `INGEST_LOOKBACK_DAYS` / `INGEST_SINCE` / `INGEST_UNTIL` 覆盖）。
- 同步内容：
  - Feishu：`npm run sync:feishu -- --since ... --until ... --limit 100 --write`
  - Get笔记：`npm run sync:getnote -- --since ... --until ... --types meeting,recorder_audio,audio --limit 200 --write`
- 脚本从本机 OpenClaw 配置读取 Get笔记凭证，从 `MEMORY.md` 读取 GBrain MCP token；不提交任何 secret。
- 本地验证运行通过：Feishu 最近 3 天 0 条；Get笔记最近 3 天导入/重写 10 条，GBrain 写入和 postprocess 正常。
- Git commit：`5c78405 Add scheduled recent sync entrypoint`，已 push。

已创建 OpenClaw cron job：
- Job ID: `71c553dd-8f45-4805-871f-8b5c7b9eefe7`
- Name: `conversation-ingest-pipeline-daily-sync`
- Schedule: `30 11,23 * * *`，timezone `Asia/Shanghai`
- 即每天 11:30 和 23:30 执行一次。
- Delivery: none；失败会 alert。

[Source: User + Tool execution, Telegram/OpenClaw, 2026-05-14]
