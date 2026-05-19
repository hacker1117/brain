---
type: concept
title: 统一会话产物摄入管道（Unified Conversation Artifact Ingestion Pipeline）
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - feishu
  - gbrain
  - getnote
  - ingestion
  - meeting
  - pipeline
---

# 统一会话产物摄入管道

Henry 在 2026-05-14 设计的核心架构理念：**不同来源（飞书、Get笔记、本地录音）共用一套 Core 处理流程，通过 Adapter 对接各自来源。**

> "我不想给每一个接入进来的 meeting，或者说是日常的一些录音的端口，都有一套自己的流程，还是希望有一套标准的流程，只不过对接不同的 Adapter。"

[Source: User, Telegram, 2026-05-14]

## 架构分层

```text
Source Adapter（不同来源）
→ Raw Artifact Store（原始产物存储）
→ Canonical Bundle（统一中间格式）
→ Router（内容路由）
→ GBrain Writer（标准化写入）
→ Post-process（实体抽取 / timeline / todos）
```

## Adapters

- **飞书 Adapter**：拉会议列表、meeting metadata、note doc、verbatim doc、minute token/妙记、录音录像
- **Get笔记 Adapter**：拉笔记列表、录音、转写（注意字段兼容：`audio.transcript || audio.original`）、总结、标签
- **本地录音 Adapter**：读取本地音频、上传/转写/生成 summary

## Canonical Bundle 格式

```json
{
  "source_type": "feishu|getnote|local_audio",
  "source_id": "...",
  "title": "...",
  "started_at": "...",
  "participants": [],
  "owner": "...",
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

## GBrain Core 共用处理流程

1. 去重：source + source_id / title + started_at
2. 生成 meeting/audio page
3. 写 frontmatter：type/title/date/source/tags/source_id
4. 写 Summary / Decisions / Action Items / Discussion Notes
5. 写 Availability Matrix
6. 保存 raw artifacts：原始 JSON、transcript 文件路径、media 文件路径
7. 抽取实体：人、公司、项目、概念
8. 建 backlinks
9. 写 timeline
10. 抽取 todos，后续进入任务池

## 内容路由规则

- 多人参与 / 有时间地点 → `meetings/`
- 个人想法/观察 → `originals/`
- 稳定框架/方法论 → `concepts/`
- 低置信度 → `inbox/review/`

两阶段路由：
1. 第一阶段：规则判断
2. 第二阶段：模糊项调用 Claude 分类（低置信度不自动入正式目录）

## Transcript 双写策略

- `~/.gbrain/imports/<source>/<source_id>/transcript.txt`：原始产物 / provenance 证据
- `~/.gbrain/sessions/feishu-YYYY-MM-DD-title.md`：供 Dream Cycle / session corpus 处理

## 代码目录结构

```text
~/projects/conversation-ingest-pipeline/  （独立 Git repo）
  src/
    cli.ts
    types.ts
    adapters/
      feishu.ts
      getnote.ts
      local-audio.ts
    core/
      raw-store.ts
      normalize.ts
      route.ts
      dedupe.ts
      render-page.ts
      write-gbrain.ts
      state.ts
    prompts/
      route-classifier.md
      meeting-normalizer.md
```

不进 repo：飞书 token、Get笔记 API key、原始 transcript、MP4/M4A、`~/.gbrain/imports/*`、`~/.gbrain/sessions/*`

## GBrain Meeting Page 标准结构

```markdown
## Context
## Availability
## Executive Summary
## Key Decisions
## Action Items
## Entity Signals
## Discussion Notes
## Raw Artifacts
## Transcript
```

## 触发方式

第一阶段：手动 CLI
```bash
node src/cli.ts sync feishu --since 2026-04-01
node src/cli.ts sync getnote --since 7d
```

第二阶段：Cron（每小时扫描新增）

第三阶段：飞书 webhook/bot 通知作为触发器

## 关键设计原则

- **导出能力优先**：不能只在来源系统内可见，必须能导出为可控 JSON/Markdown/transcript/media
- **按内容主语归档，不按来源归档**：飞书/Get笔记只是 source
- **Transcript 是 ground truth**：AI 总结可以作为输入，但 GBrain 要自己的 canonical 结构
- **字段兼容**：Get笔记 transcript = `audio.transcript || audio.original`
