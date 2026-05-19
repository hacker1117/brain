---
type: concept
title: Unified Conversation Artifact Ingestion Architecture
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - feishu
  - gbrain
  - getnote
  - ingest-pipeline
  - meetings
---

# Unified Conversation Artifact Ingestion Architecture

设计原则：**不要针对每个来源各做一套流程**，而是 Adapter + Canonical Core 分离。

## 核心架构

```
Source Adapter（来源不同）
→ Raw Artifact Store（~/.gbrain/imports/<source>/<id>/）
→ Canonical Bundle（统一中间格式）
→ Router（分类路由）
→ GBrain Writer（MCP HTTP）
→ Post-process（实体/项目/backlink/timeline）
```

## Source Adapters

- `adapters/feishu.ts`：飞书会议记录、妙记、智能纪要、逐字稿、音视频
- `adapters/getnote.ts`：Get笔记录音/笔记，兼容 `audio.transcript || audio.original`
- `adapters/local-audio.ts`：本地音频

## Canonical Bundle 格式

```json
{
  "source_type": "feishu|getnote|getseed|local_audio",
  "source_id": "...",
  "title": "...",
  "started_at": "...",
  "participants": [],
  "artifacts": {
    "summary": "...",
    "chapters": [],
    "todos": [],
    "transcript_path": "...",
    "media_path": "..."
  },
  "availability": {
    "summary": true,
    "transcript": true,
    "media": false,
    "failures": []
  },
  "classification": {
    "route": "meeting|personal_thought|concept|raw_source",
    "confidence": 0.9
  }
}
```

## GBrain Page 写入结构（会议）

slug 格式：`meetings/YYYY-MM-DD-<title>`

```markdown
## Context
## Availability Matrix
## Executive Summary
## Key Decisions
## Action Items
## Entity Signals
## Discussion Notes
## Raw Artifacts (paths only, no transcript dump)
## Transcript Index
```

- **Transcript 不塞正文**，只保留路径索引
- 全文存：`~/.gbrain/imports/<source>/<id>/transcript.txt`
- 同时双写：`~/.gbrain/sessions/<date>-<source>-<slug>.md`（供 Dream Cycle 读取）

## 路由规则

| 内容类型 | 路由 |
|----------|------|
| 会议/访谈 | `meetings/` |
| 个人想法 | `originals/` |
| 稳定框架 | `concepts/` |
| 低置信度/原始资料 | `inbox/review/` |

## 重复处理

1. 原始文件层：`source_type + source_id` 做目录，重跑覆盖同一路径
2. GBrain 页面层：slug 确定性生成，重跑 update 同一页
3. 历史旧 slug：normalizer 标记 duplicate，自动 soft-delete
4. link/timeline：插入前先查已有，幂等

## 代码位置

```text
~/.openclaw/workspace/ingest-pipeline/
  src/
    cli.ts
    types.ts
    adapters/
    core/
      raw-store.ts
      normalize.ts
      route.ts
      dedupe.ts
      render-page.ts
      write-gbrain.ts  ← 直接调 GBrain HTTP/MCP，不走 CLI shell
      state.ts
    prompts/
```

GitHub: `https://github.com/hacker1117/conversation-ingest-pipeline` (private)

## 触发方式

- 当前：手动 `npm run sync:feishu -- --since 2026-04-01 [--write]`
- 计划中：每小时 cron，自动拉最近 48h + 补漏最近 7 天
- 后期：飞书 Webhook 触发（作为"有新内容"信号，实际内容仍主动拉）

## 关键设计决策（2026-05-14）

- writer 改为直接走 GBrain HTTP/MCP（不走 shell 调 gbrain CLI，避免卡住）
- transcript 双写：imports 保留来源证据，sessions 供 Dream Cycle
- 按内容主语归档，不按来源归档

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
