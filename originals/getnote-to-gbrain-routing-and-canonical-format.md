---
type: original
title: Get笔记到 GBrain 的路由判断与 canonical meeting 格式原则
date: '2026-05-14T00:00:00.000Z'
tags:
  - classification
  - gbrain
  - getnote
  - ingestion
  - meetings
---

Henry 追问：Get笔记导入时如何判断一条录音是会议访谈，还是他自己的想法；这个过程中需要引入什么机制判断；以及 Get笔记的会议纪要或 AI 总结是否可以直接放到 `meetings/`，还是需要标准格式化处理，方便后续应用。

建议机制：采用“两阶段路由”。第一层用可解释规则判断：`note_type=meeting`、标题/标签中的“访谈/会议/沟通/调研/需求/对接/复盘/客户/主管/岗位”、多人对话痕迹、第一人称独白痕迹、speaker diarization / attendees / duration 等信号打分。第二层只对模糊项调用 Claude Sonnet 分类，输出固定 JSON：`route`、`confidence`、`reason`、`target_slug_prefix`、`needs_human_review`。低置信度内容先进入 `inbox/getnote-review/`，不自动入正式目录。

对会议纪要/AI 总结的判断：不能原样直接当 `meetings/` canonical page。Get笔记 AI 总结可以作为输入，但 GBrain 需要标准结构以服务检索、实体传播、行动项追踪。推荐统一格式包括 frontmatter、Context、Executive Summary、Key Decisions、Action Items、Entity Signals、Discussion Notes、完整 Transcript。Transcript 是 ground truth；Get笔记总结是参考；GBrain adapter 再做结构化归档，但不过度做战略分析，深层模式留给 Dream Cycle / synthesis。

[Source: User, Telegram, 2026-05-14]
