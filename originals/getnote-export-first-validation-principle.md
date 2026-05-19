---
type: original
title: Get笔记接入 GBrain 的核心验证点：必须可导出
date: '2026-05-14T00:00:00.000Z'
tags:
  - export
  - gbrain
  - getnote
  - integration
  - transcript
---

Henry 同意先用一条录音、一条 Transcript 和一条会议纪要跑通 Get笔记到 GBrain 的最小闭环。他强调核心验证点是：这些内容不能只在 Get笔记里面或 Skill 里面可见，还必须能被导出成可控的数据/文件，否则无法稳定进入 GBrain 后续处理链路。

下一步需要完成 Get笔记授权，然后验证：最近笔记列表能否读取；笔记详情能否拿到正文、转写、总结、音频元数据；能否导出到本地文件，再分别进入 `~/.gbrain/sessions` 或 `gbrain__put_page`。

[Source: User, Telegram, 2026-05-14]
