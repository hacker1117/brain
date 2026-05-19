---
type: project
title: Conversation Ingest Pipeline 实际写入 GBrain 测试
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - getnote
  - ingestion
  - write-test
---

# Conversation Ingest Pipeline 实际写入 GBrain 测试

## 背景

Henry 要求先把已通过飞书和 Get笔记导出的内容按脚本实际运行一遍，把 GBrain writer 从 dry-run 切到真实写入，先看效果；live API adapter、cron、Claude 分类兜底后续再做。

[Source: User, Telegram, 2026-05-14]

## 执行

修复 writer：`src/core/write-gbrain.ts` 从错误的 `gbrain put <slug> --stdin` 改为实际 CLI 支持的 `gbrain put <slug> --content <markdown>`。

运行：

```bash
npm run check
npm run sync:feishu -- --fixture existing --write
npm run sync:getnote -- --fixture existing --write
```

## 结果

成功写入 4 个页面：

1. `meetings/2026-04-29-caip-内饰板研发学习`
   - source: feishu
   - route: meeting
   - transcript 双写成功
   - media path 已记录

2. `meetings/2026-04-25-516沙龙分享沟通`
   - source: feishu
   - route: interview
   - transcript 双写成功
   - media unavailable reason 已记录

3. `concepts/2026-05-11-基于excel的结构化数据汇总与分析skill设计思路`
   - source: getnote
   - route: concept
   - transcript 双写成功

4. `meetings/2026-05-11-仓库主管工作职责与ai工具应用需求访谈`
   - source: getnote
   - route: interview
   - transcript 双写成功

Git commit：`d4beb1d Enable actual gbrain put writer`。

## 观察到的问题

1. 页面可写入、可检索、可 embedding，基础链路成立。
2. 当前 render 把 transcript 前 12000 字直接放入 GBrain page，页面偏长。后续建议改成：page 只放 transcript 摘要/路径，全文留在 `~/.gbrain/sessions` 和 `~/.gbrain/imports`。
3. Feishu/Getnote 的 AI summary 原样进入 Executive Summary，格式还不够 canonical，需要后续 normalizer 清洗。
4. 目前未做实体抽取、项目关联、timeline/backlinks，后续在 core post-process 里补。
5. 已出现与之前手工写入会议页并存的情况，后续需要 dedupe/slug policy，避免同一会议重复页面。

[Source: Tool execution, OpenClaw, 2026-05-14]
