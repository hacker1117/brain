---
type: idea
title: 2026 05 14 Unified Ingest Pipeline Architecture 185e90
date: '2026-05-14T00:00:00.000Z'
tags:
  - adapter-pattern
  - architecture
  - conversation-ingest
  - feishu
  - gbrain
  - getnote
  - ingest-pipeline
---

# Unified Conversation Artifact Ingestion Pipeline：一套 Core，多个 Adapter

**Session:** 2026-05-14 07:07–08:48 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `185e90`)

## 核心设计原则

Henry 在讨论飞书和 Get笔记两套接入时，提出了最核心的架构要求：

> 「你推荐的这个流程和咱们在另外一个地方聊到的GetFeed的笔记的存储流程是否从机制上是一致的呢？然后是不是有一些流程是可以共用的，但是另外一些部分写成不同的adapt呢？我想请你整体考虑一下这件事。一方面是我们要符合gBrain的要求和处理标准，方便之后更好地提取和应用。另外一方面，我不想给每一个接入进来的meeting，或者说是日常的一些录音的端口，都有一套自己的流程，还是希望有一套标准的流程，只不过对接不同的Adapter。」

**结论：Adapter 不同，Core 共用。**

## 架构层级

```text
Source Adapter（不同来源）
→ Canonical Bundle（统一中间格式）
→ GBrain Ingestion Core（统一处理流程）
→ GBrain DB（canonical pages / chunks / facts / links / timeline）
```

## 当前实现：conversation-ingest-pipeline

**Git repo:** `https://github.com/hacker1117/conversation-ingest-pipeline`（private）

```
src/
  adapters/
    feishu.ts       # 飞书 lark-cli adapter
    getnote.ts      # Get笔记 API adapter
    local-audio.ts  # 本地录音 adapter
  core/
    raw-store.ts    # 原始产物存储
    normalize.ts    # canonical bundle 生成
    route.ts        # 路由分类（speaker count + 标题规则）
    render-page.ts  # GBrain 页面渲染
    write-gbrain.ts # GBrain MCP writer（走 HTTP/MCP，非 shell CLI）
    sessions.ts     # transcript 双写到 ~/.gbrain/sessions/
    state.ts        # 去重 / idempotency
  types.ts          # ConversationBundle 统一结构
  cli.ts
```

## Canonical Bundle 结构

```json
{
  "source_type": "feishu|getnote|local_audio",
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
    "route": "meeting|interview|personal_thought|concept|raw_source",
    "confidence": 0.9
  }
}
```

## GBrain 页面结构（会议类）

```markdown
## Context
## Availability Matrix
## Executive Summary
## Key Decisions
## Action Items
## Entity Signals
## Discussion Notes
## Transcript Index  ← 只写路径，不 dump 全文
## Raw Artifacts
```

**关键原则：** Transcript 不 dump 进 GBrain 正文，只写索引路径。全文保留在：
- `~/.gbrain/imports/<source>/<source_id>/transcript.txt`
- `~/.gbrain/sessions/<date>-<source>-<slug>-<id>.md`（供 Dream Cycle 使用）

## 路由规则（按优先级）

1. **讲话人数量强规则**（见 [[wiki/originals/ideas/2026-05-14-speaker-count-as-routing-signal-2338b7]]）
   - `speakerCount >= 2` → meeting/interview
   - `speakerCount === 1` → concept/personal_thought

2. **标题强信号规则**
   - 含「访谈 / 调研 / 工作内容 / 痛点 / 岗位职责」→ interview
   - 含「我有一个想法 / 框架 / 方法 / 系统」→ concept

3. **低置信度兜底**
   - → `inbox/review/...`

4. **Claude 兜底分类**（暂未实现，记为后续可扩展项）

## 产物存储分层

```text
~/<git-repo>/                  # 代码（可移植，进 Git）
~/.gbrain/imports/             # 原始产物（不进 Git）
  feishu/<meeting_id>/
  getnote/<note_id>/
    raw.json
    transcript.txt
    media.mp4/m4a（如有）
    bundle.json
~/.gbrain/sessions/            # Dream Cycle 可消费的 transcript
GBrain DB                      # canonical pages / entities / timeline
```

## GBrain Writer 实现选择

Writer 走 **GBrain HTTP/MCP**，不 shell 调 `gbrain CLI`。  
原因：shell CLI 在 agent 上下文中处理中文 slug / 长 context 时会卡住；MCP 接口是幂等的，加 link / timeline 前先查已有记录。

## 定时同步

**cron job:** 每天 11:30 + 23:30 Asia/Shanghai

```bash
npm run sync:recent
# 自动同步最近 3 天：Feishu meetings + Getnote meeting,recorder_audio,audio
```

**commit:** `5c78405 Add scheduled recent sync entrypoint`

## 与 Get笔记 note_type 问题的关联

初版 adapter 只拉 `note_type=meeting`，导致 14 条中只读到 2 条。修复后拉取 `meeting,recorder_audio,audio`，speaker count 规则弥补了 metadata 字段不准确的问题。详见 [[wiki/personal/reflections/2026-05-14-getnote-type-filter-and-pipeline-launch-185e90]]。

---
*Related: [[wiki/personal/reflections/2026-05-14-pipeline-render-quality-and-gbrain-canonical-structure-9890b4]] · [[wiki/originals/ideas/2026-05-14-speaker-count-as-routing-signal-2338b7]] · [[wiki/originals/ideas/2026-05-14-ingest-pipeline-git-repo-and-dream-cycle-session-design-9890b4]] · [[originals/lark-cli-feishu-integration-setup-2026-05-14]]*
