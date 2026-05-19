---
type: original
title: 为 GBrain 建立 OpenClaw Session Transcript Exporter
date: '2026-05-14T00:00:00.000Z'
tags:
  - automation
  - dream
  - gbrain
  - openclaw
  - signal-capture
  - transcript
---

Henry 决定补齐 OpenClaw 日常对话到 GBrain Dream / Signal Capture 的闭环：写 exporter 将 OpenClaw session JSONL 转成标准 transcript，并同步配置 Dream Cycle 与 Signal Capture cron。

## Henry's exact words

> 好的，那你就来写这个exporter吧，就把对应的这个OpenClaw里的session数据，然后做成一个标准的transcript。同时把Dreamcycle里必要的配置也做好。还有signal capture这个Crone job里面需要修改的地方也都修改一遍。

## Implementation note

Exporter path: `/Users/lihengyi/.gbrain/scripts/export-openclaw-sessions.py`。
Transcript corpus path: `/Users/lihengyi/.gbrain/sessions`。
Signal Capture cron 已改为先导出 transcript，再从 transcript corpus 抽取 signal。Dream Cycle cron 已改为先导出 transcript，再运行 `gbrain dream --json`。

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
