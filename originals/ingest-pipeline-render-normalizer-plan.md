---
type: original
title: Ingest Pipeline Render/Normalizer 改进计划
date: '2026-05-14T00:00:00.000Z'
tags:
  - backlinks
  - dedup
  - entity-extraction
  - gbrain
  - ingest-pipeline
  - meeting
  - normalizer
  - timeline
---

# Ingest Pipeline Render/Normalizer 改进计划

## 背景

首次真实写入（`--write` flag）完成后发现，当前 render-page.ts 把 transcript 全文前 12000 字塞进正文，导致 GBrain page 变成 transcript dump。需要改进。

## 改进项

### 1. Transcript 不进正文

正文只保留：
- transcript 存储路径（`~/.gbrain/imports/<source>/<id>/transcript.txt`）
- transcript 字数/时长摘要

全文留在：
- `~/.gbrain/imports/<source>/<id>/transcript.txt`（原始 artifact）
- `~/.gbrain/sessions/<date>-<source>-<slug>-<id>.md`（Dream Cycle 可用）

### 2. 添加 Normalizer 清洗

来源 AI summary 不能原样进入 GBrain canonical page。需要 normalizer 把内容转成标准结构：

```markdown
## Executive Summary
## Key Decisions
## Action Items
## Entity Signals
## Discussion Notes
## Availability
## Raw Artifacts
```

第一阶段：规则清洗（章节提取、格式化）
第二阶段：Claude 语义清洗兜底

### 3. 实体抽取 + 项目关联

从 summary/transcript 中抽取：
- 人名 → 建 `people/` backlink
- 公司/项目 → 建 `companies/` / `projects/` backlink
- 关键概念 → 建 `concepts/` backlink

### 4. Timeline 写入

每场会议提取时间点，写入涉及实体的 timeline。

### 5. Backlinks

所有提到的 people / companies / projects 从 meeting page 建出向 link。

### 6. Dedup / Slug 策略

与之前手工写的会议页做去重：
- 按 `source_id` 匹配已有页面
- 已存在则 update，不 create
- slug 策略：`meetings/YYYY-MM-DD-<normalized-title>`

## 当前已写入的 4 个页面

- `meetings/2026-04-29-caip-内饰板研发学习`
- `meetings/2026-04-25-516沙龙分享沟通`
- `concepts/2026-05-11-基于excel的结构化数据汇总与分析skill设计思路`
- `meetings/2026-05-11-仓库主管工作职责与ai工具应用需求访谈`

## 下一步

按 Henry 指示：改 render-page.ts 限制 transcript 大小 + 加 normalizer 清洗 + 实体抽取 + timeline/backlinks + dedup。

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
