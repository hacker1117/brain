---
type: original
title: Get笔记导入 GBrain 的分层路由原则
date: '2026-05-14T00:00:00.000Z'
tags:
  - gbrain
  - getnote
  - ingestion
  - meetings
  - transcript
---

Henry 询问 GBrain 中 `meetings/` 文件夹的作用，以及如果参考 GBrain 相关做法和概念，Get笔记拿到的录音、transcript 和会议纪要应该分别放在哪里。

结论：`meetings/` 是每一场会议的 canonical meeting page，存放会议标题、日期、参会人、Summary、Key Decisions、Action Items、Discussion Notes 和完整 Transcript。但 meeting page 不是终点，会议摄入后还必须把相关信息传播到 `people/`、`companies/`、`projects/`、`concepts/` 等实体页面，并建立 timeline 与 backlinks。

Get笔记导入建议采用分层路由：原始 API JSON 与音频元数据先保留在 `~/.gbrain/imports/getnote/` 作为 staging/provenance；完整 transcript 同时进入 meeting page 的 `## Transcript` 与 `~/.gbrain/sessions/` 供 Dream Cycle 深度处理；会议纪要/content 字段进入 `meetings/YYYY-MM-DD-<slug>`；非会议型个人思考录音按主语进入 `originals/` 或 `concepts/`；行动项先进入 meeting page 的 `## Action Items`，后续再任务化。

[Source: User, Telegram, 2026-05-14]
