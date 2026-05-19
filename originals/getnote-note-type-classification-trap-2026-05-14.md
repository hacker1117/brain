---
type: original
title: Get笔记 note_type 分类陷阱：recorder_audio ≠ meeting
date: '2026-05-14T00:00:00.000Z'
tags:
  - api-design
  - getnote
  - ingestion
  - lesson
  - pipeline
---

# Get笔记 note_type 分类陷阱：recorder_audio ≠ meeting

## 发现背景

在执行 `sync:getnote --types meeting --since 2026-05-01` 时，5月份只导入了 2 条笔记。Henry 直接指出：「我的 Get笔记里，至少存在10篇以上的5月份以后的记录。」

## 根因

Get笔记 API 返回的 `note_type` 字段，实际类型分布与直觉**不一致**：

| 类型 | 含义 | 数量 |
|------|------|------|
| `meeting` | 飞书/Zoom 等平台会议 | 2 |
| `recorder_audio` | 录音笔/手机录音 + AI 转写，**含大量访谈/调研** | 9 |
| `audio` | 普通音频笔记 | 2 |
| `recorder_flash_audio` | 闪记/碎片录音 | 1 |

**问题**：ingest pipeline 的初始 filter `--types meeting` 只拉到 `note_type=meeting` 的 2 条，而大量真实"会议/访谈录音"实际类型是 `recorder_audio`，被完全遗漏。

## 修复

1. **Adapter filter 扩展**：改为 `--types meeting,recorder_audio,audio`
2. **Router 修正**：对标题含「访谈 / 调研 / 工作内容 / 工作流程 / 痛点 / 岗位职责 / 提效需求」的 `recorder_audio`，优先路由为 `meeting/interview`，而不是 `raw_source`
3. **兼容字段**：`transcript = audio.transcript || audio.original`（Get笔记 API 文档说在 `audio.transcript`，但实际样本里在 `audio.original`）

修复后导入了 13 条，包含了 5 月 11 日的所有生产岗位访谈。

## 教训

> **外部 API 的数据分类（type/kind 字段）和人类直觉往往不一致。**  
> 不要假设"会议"就是 `meeting` 类型，需要实际调 API 看 type 分布，再设计 filter。  
> 这和"隐藏层"模式一致：第一次真实测试才暴露分类假设的错误。

## 关联

- [[concepts/unified-conversation-artifact-ingestion-architecture]]
- [[projects/conversation-ingest-pipeline-implementation]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
