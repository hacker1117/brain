---
type: original
title: GBrain Dream Cycle Synthesize Failure — Unicode/NUL 三层修复
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - unicode
---

## 问题

2026-05-15 调试中发现 GBrain Deep Dream Cycle 的 synthesize 阶段连续失败，表现为：

1. 第一层：`surrogates not allowed` — emoji 说话人标记（如 `🎤`）经过 JS → JSON → LiteLLM/Python 链路时被还原成非法 surrogate pair
2. 第二层：`unsupported Unicode escape sequence` — 某个 OpenClaw/GWC transcript 里有 `GPT\u00004o`，导出后变成真实 NUL 字符，Postgres `jsonb` 不接受 `\u0000`
3. dry-run 会过、真实写入时炸，因为 dry-run 不触发 job 序列化到 Postgres

## 修复方向

- `export-openclaw-sessions.py`：清理 `\u0000`、真实 NUL、非 BMP emoji、surrogate
- `ingest-pipeline` transcript writer：同样清理
- `gbrain/src/core/cycle/transcript-discovery.ts`：synthesize 入口兜底清理

> 核心判断：不是单个文件的问题，是整个 transcript 进入 synthesize 的链路缺乏防御，必须生成端 + 读取端双保险。

[Source: User, OpenClaw Transcript, 2026-05-15]
