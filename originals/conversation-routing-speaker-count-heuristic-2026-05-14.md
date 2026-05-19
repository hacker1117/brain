---
type: original
title: 会话路由：讲话人数量作为分类强信号
date: '2026-05-14T00:00:00.000Z'
tags:
  - conversation-routing
  - heuristics
  - meeting-ingestion
  - pipeline
---

# 会话路由：讲话人数量作为分类强信号

Henry 提出的路由规则（补充到 conversation-ingest-pipeline router）：

> 如果这个会议里面的讲话人就只有一个，那大概率就是只有我自己，那就会是一些思考、想法、灵感之类的记录。那如果讲话人不止我一个，那大概率就是一个对谈、访谈或者会议等等，我觉得都可以按照会议处理。

## 规则实现

- `speakerCount >= 2` → 强加 meeting score → 路由为 `meeting/interview`
- `speakerCount === 1` → 强加 thought score → 路由为 `concept/personal_thought`

比标题规则更接近内容本质，作为 **强规则层** 加入 router。

已提交：`8af48e8 Use speaker count for conversation routing`

## Claude 兜底分类

Henry 的补充决策：

> Cloud 的兜底就记录为一个后续可以考虑的拓展项吧。暂时我们就不实现了。

当 rules 置信度低时用 Claude 判断类型 — 作为后续扩展项记录，暂不实现。

## 关联

- [[projects/conversation-ingest-pipeline]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
