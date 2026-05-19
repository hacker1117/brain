---
type: original
title: Ingest Pipeline 的两个架构决策：Git Repo + Transcript 双写
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - gbrain
  - git
  - pipeline
  - portability
  - transcript
---

# Ingest Pipeline 的两个架构决策：Git Repo + Transcript 双写

## 决策 1：代码做成独立 Git Repo，不只放 OpenClaw Workspace

Henry 提出：「我能不能把这一套的代码，不止放在 OpenClaw 的 Workspace 里，同时也作为一个 repo 来放到 Git 里面管理呢？这样之后如果我还有其他的电脑上要装 OpenClaw，都可以用我这次写完的这一套代码。」

**决策**：是，且应该这么设计。

- Repo 路径：`~/.openclaw/workspace/ingest-pipeline/`（自身是独立 Git repo）
- 已推送：`https://github.com/hacker1117/conversation-ingest-pipeline`（private）
- **进 repo 的**：代码、prompts、config.example.json、README
- **不进 repo 的**：token、API key、原始 transcript、MP4/M4A、`~/.gbrain/imports/*`、`~/.gbrain/sessions/*`

换电脑只需：`git clone → npm install → cp config.example.json config.json → 重新授权`

## 决策 2：Transcript 双写到两个位置

Henry 问：「这个过程中产生的一些 transcript 是不是也都会放到对应的 session 目录下？后续会被 Dream Cycle 调用呢？」

**决策**：双写，各有用途。

```text
~/.gbrain/imports/<source>/<source_id>/transcript.txt
```
→ raw artifact / provenance，保留来源证据。

```text
~/.gbrain/sessions/<date>-<source>-<slug>-<id>.md
```
→ 供 Dream Cycle / session corpus 后续处理。

**关键点**：Dream Cycle 不会自动看任意 imports 目录。只写 imports 不够，必须同时写到 sessions 目录才能进入 Dream Cycle 视野。

## 最终分层设计

```text
Git repo：代码和模板（可迁移）
~/.gbrain/imports：原始产物（来源证据）
~/.gbrain/sessions：transcript（Dream Cycle 可读）
GBrain DB：canonical page / chunks / facts / links / timeline
```

## 关联

- [[concepts/unified-conversation-artifact-ingestion-architecture]]
- [[projects/conversation-ingest-pipeline-implementation]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
