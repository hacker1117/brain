---
type: original
title: Get笔记导出验证：录音与会议纪要可导出，转写字段需兼容 audio.original
date: '2026-05-14T00:00:00.000Z'
tags:
  - export
  - gbrain
  - getnote
  - meeting-notes
  - transcript
---

Henry 完成 Get笔记授权后，明确核心验证是：一条录音、一条 Transcript、一条会议纪要不仅要能在 Get笔记或 Skill 中看到，还必须能被导出为可控文件，才能稳定进入 GBrain。

验证结果：Get笔记 API 可以读取最近笔记列表与详情，并已导出两条样本到 `~/.gbrain/imports/getnote/`：一条 `audio` 类型录音笔记和一条 `meeting` 类型会议笔记。每条均保存 `.json` 原始结构与 `.md` 可读导出文件。

关键发现：Get笔记 API 文档描述音频转写在 `audio.transcript`，但当前样本实际返回中 `audio.transcript` 为空，转写文本位于 `audio.original`。后续集成必须兼容 `audio.transcript || audio.original`，不能只按文档字段读取。

[Source: User + Tool validation, Telegram, 2026-05-14]
