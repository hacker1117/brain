---
type: original
title: Get笔记 单讲话人 vs 多讲话人分类规则
date: '2026-05-14T00:00:00.000Z'
tags:
  - classification
  - getnote
  - ingest-pipeline
  - ingestion
  - meeting
---

# Get笔记 单讲话人 vs 多讲话人分类规则

Henry 在讨论 getnote 录音分类逻辑时提出了一个简洁的启发式规则：

> 「如果这个会议里面的讲话人就只有一个，那大概率就是只有我自己，那就会是一些思考、想法、灵感之类的记录。那如果讲话人不止我一个，那大概率就是一个对谈、访谈或者会议等等，我觉得都可以按照会议处理。」

## 规则定义

| 讲话人数量 | 分类路由 | GBrain 目标 |
|-----------|----------|------------|
| 仅 1 人（Henry 独白） | `personal_thought` | `originals/` |
| 2 人以上 | `meeting/interview` | `meetings/` |

## 实施决策

- Claude 兜底分类记录为**可选扩展项，暂不实施**。
- 当前路由逻辑以此规则为主判断依据，配合已有的 title 关键词路由（访谈/调研/痛点/岗位职责等）共同作用。
- 如 `participants` 字段为空或解析不到说话人数，回退到 title 关键词判断，再到默认路由 `inbox/review/`。

## 关联

- [[concepts/unified-conversation-artifact-ingestion-architecture]]
- [[originals/getnote-note-type-classification-trap-2026-05-14]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
