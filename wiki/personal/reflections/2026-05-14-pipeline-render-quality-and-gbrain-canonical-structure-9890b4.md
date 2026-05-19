---
type: reflection
title: 会议录入页质量批判：Transcript 不能 Dump 进 GBrain，以及 Canonical Page 结构的要求
date: '2026-05-14T00:00:00.000Z'
tags:
  - deduplication
  - feishu
  - gbrain
  - getnote
  - ingestion-pipeline
  - normalizer
  - reflection
  - render-strategy
---

# 会议录入页质量批判：Transcript 不能 Dump 进 GBrain，以及 Canonical Page 结构的要求

**Session:** 2026-05-14 07:40–07:44 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `9890b4`)

## 背景

[[projects/conversation-ingest-pipeline-implementation]] 从 dry-run 切到真实写入后，写入了 4 条 GBrain 页面，暴露了一批 render 策略问题。Henry 随后明确要求按以下方向改。

## 观察到的问题

第一批真实写入后，Agent 自己总结了以下问题：

1. **Transcript 直接塞进正文**  
   当前 `render-page.ts` 把逐字稿前 12,000 字放进 GBrain page 正文，导致页面体积极大。这是错误的。GBrain page 应该是「认知摘要 + 路径索引」，不是 transcript dump。

2. **AI summary 原样进入 Executive Summary**  
   飞书/Get笔记的 AI 总结格式不统一、质量参差，原样进入 GBrain 不符合 canonical 结构要求。需要 normalizer 清洗。

3. **缺实体抽取、项目关联、timeline、backlinks**  
   写入的页面是孤岛，没有链接到 `people/`、`companies/`、`projects/`，没有 timeline 条目，没有 backlink。

4. **会与已有手工写入的页面重复**  
   部分会议已经有人工写入的 GBrain 页，pipeline 写入产生了重复页面，需要 dedupe + slug policy。

## Henry 的要求（原话）

> 「我觉得按照你说的改吧。meeting那个配置里面不应该包含大量的transcript。同时你还要加上对应的Normalizer，然后以及实体抽取、项目关联、timeline、backlink等等。然后还有对应的你之前协助的重复的会议纪要的相关的处理。」

## Canonical GBrain 会议页应有的结构

参见 [[concepts/unified-conversation-artifact-ingestion-architecture]]：

```markdown
## Context          ← 会议元数据（日期、参会人、来源）
## Availability     ← 产物可获取性矩阵（纪要/逐字稿/录音/视频）
## Executive Summary ← normalizer 清洗后的摘要，非来源 AI summary 原样
## Key Decisions     ← 显式决策列表
## Action Items      ← 待办（关联 owner、关联项目）
## Entity Signals    ← 人名、公司名、项目、概念（链接到对应 GBrain 页）
## Discussion Notes  ← 精炼后的讨论要点
## Raw Artifacts     ← transcript 路径、media 路径——路径只有，不塞内容
## Transcript Index  ← 章节+时间戳索引，正文不放全文
```

**核心原则：GBrain page 是认知资产，不是文件备份。**  
Transcript 全文存 `~/.gbrain/imports/<source>/<id>/transcript.txt`，  
同时双写 `~/.gbrain/sessions/<date>-<source>-<slug>.md`（供 Dream Cycle 使用）。

## Normalizer 的职责

1. 把飞书/Get笔记 AI summary 转成统一 GBrain canonical section 格式
2. 标准化参会人列表（中文名 → 已知 `people/` 页 slug）
3. 清洗章节标题格式
4. 提取显式决策 vs. 讨论事项（分类）
5. 提取 Action Items 并标注 owner

## 实体抽取要求

- 人名 → 查 `wiki/people/` 已有页，写 backlink，写 timeline 条目
- 公司/组织 → 查 `companies/` / `entities/`，建立关联
- 项目 → 查 `projects/`，写 timeline 条目
- 新实体 → 标记为「未知实体」，不自动创建页面，等人工确认

## Deduplication 策略

[[concepts/unified-conversation-artifact-ingestion-architecture]] 已定义四层 dedup，本次写入验证了它的必要性：

1. `source_type + source_id` 做 import 目录（重跑覆盖）
2. slug 确定性生成（重跑 update 同一页，不新建）
3. 历史旧 slug：normalizer 标记 duplicate，自动 soft-delete
4. link/timeline 插入前先查已有，幂等

---
*Related: [[concepts/unified-conversation-artifact-ingestion-architecture]] · [[projects/conversation-ingest-pipeline-implementation]] · [[projects/conversation-ingest-pipeline-write-test-2026-05-14]] · [[originals/feishu-getnote-gbrain-ingestion-pipeline]]*
