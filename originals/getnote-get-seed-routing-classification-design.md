---
type: original
title: Get笔记内容路由与分类设计思考
date: '2026-05-14T00:00:00.000Z'
tags:
  - classification
  - gbrain
  - getnote
  - getseed
  - ingestion
  - routing
---

# Get笔记内容路由与分类设计思考

Henry 在 2026-05-14 Getseed Topics 中提出的核心问题和设计思路。

[Source: User, Telegram, 2026-05-14]

## 核心问题

> "你怎么来判断是会议访谈，还是我自己记录的一些想法呢？在这个过程中，你要引入什么样的机制来判断呢？另外还有一个问题，就是会议纪要或者 AI 的总结是否直接能放到 Meetings 的配置里呢？它会不会需要一些标准的格式化的处理，方便以后更好的应用呢？"

## 路由分类机制

**规则优先（第一阶段）：**
- 多人参与 / 有参会人列表 / 会议标题关键词 → `meetings/`
- 单人、时间短、语气是自言自语/记录想法 → `originals/`
- 包含定义/框架性内容 → `concepts/`
- 无法判断 → `inbox/review/`

**Claude 分类兜底（第二阶段）：**
- 仅用于规则无法确定的模糊项
- 低置信度不自动入正式目录

## Get笔记字段兼容注意

Get笔记文档说 transcript 在 `audio.transcript`，但真实样本里在 `audio.original`：
```ts
const transcript = note.audio?.transcript || note.audio?.original
```

## AI 总结处理原则

- 第三方 AI 总结（飞书/Get笔记生成的）：作为 `artifacts.summary` 存入，不直接当 GBrain canonical content
- GBrain 自己处理：基于 transcript/summary 生成标准格式页面
- 标准 GBrain meeting page 结构：Context / Availability / Executive Summary / Key Decisions / Action Items / Entity Signals / Discussion Notes / Raw Artifacts / Transcript

## 关联

- 统一架构：[[concepts/unified-conversation-artifact-ingestion-pipeline]]
- Get笔记技能来源：`https://clawhub.ai/iswalle/getnote`
