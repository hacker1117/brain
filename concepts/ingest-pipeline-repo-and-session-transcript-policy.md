---
type: concept
title: Ingest Pipeline 的 Git 管理与 Dream Cycle transcript 策略
date: '2026-05-14T00:00:00.000Z'
tags:
  - dream-cycle
  - gbrain
  - git
  - ingestion
  - openclaw
  - transcripts
---

# Ingest Pipeline 的 Git 管理与 Dream Cycle transcript 策略

Henry 确认两点：

> “第一个是我能不能把这一套的代码，不止放在OpenClaw的Workspace里，同时也作为一个repo来放到Git里面管理呢？这样之后如果我还有其他的电脑上要装OpenClaw，都可以用我这次写完的这一套代码。第二个是这个过程中产生的一些transcript是不是也都会放到对应的session目录下？后续会被Dream Cycle调用呢？”

结论：

1. ingest-pipeline 应该作为独立 Git repo 管理，而不是只放在 OpenClaw workspace。代码、schema、prompts、adapter、cron 配置模板进入 repo；API key、tokens、raw transcripts、media 不进 repo。
2. transcript 应该双写：一份进入 `~/.gbrain/imports/<source>/<id>/` 作为 raw artifact/provenance；一份规范化写入 `~/.gbrain/sessions/`，供 GBrain session corpus / Dream Cycle 后续处理。
3. 需要注意：Dream Cycle 不会自动处理任意 imports 目录；只有写到 session corpus 目录并符合后续读取约定，才稳定进入 Dream Cycle 视野。

[Source: User, Telegram, 2026-05-14]
