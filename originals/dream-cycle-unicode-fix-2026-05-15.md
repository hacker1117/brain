---
type: original
title: Dream Cycle Synthesize 失败根因排查与修复决策
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - openclaw
  - unicode
---

Henry 发现 13:00 cron 没有可见产出，主动要求排查并按建议修复。整个过程是一次典型的 infra 调试链路。

## 根因链（按发现顺序）

1. **第一层：delivery 配置为 `none`** → Dream Cycle 完成后不发 Telegram，无感知
2. **第二层：surrogate 字符** → Get笔记/录音 transcript 里的说话人标记 emoji 经 JS → JSON → LiteLLM/Python 链路变成非法 surrogate，导致 `surrogates not allowed`
3. **第三层：`\u0000` / NUL 字符** → 一个 OpenClaw/GWC transcript 里有 `GPT\u00004o`，Postgres `jsonb` 不接受 `\u0000`，dry-run 不写 job 所以通过、真实跑时炸

## 修复决策

Henry 明确指示：
> "好的，就按照你建议的两件事来做。同时你也要判断一下这个 transcript 的来源是哪里，是 Telegram 还是 Meetings 的信息。"

两件事：
1. 把 13:00 Dream Cycle 的 delivery 改成 Telegram announce
2. 清理坏 Unicode transcript，rerun Dream Cycle 验证

修复层：
- `export-openclaw-sessions.py`：清理 `\u0000`、NUL、非 BMP emoji、surrogate
- `ingest-pipeline` transcript writer：同步清理
- `gbrain/src/core/cycle/transcript-discovery.ts`：synthesize 入口兜底

## 关键观察

> "找到真正触发点了：不是 Telegram，也不是 OpenClaw 本身的普通文本；是 Get笔记/录音 transcript 里的说话人标记 emoji"

经过三层修复，dry-run 确认 synthesize 从 fail → ok（34 个 transcript，26 个进入合成）。

[Source: User, Telegram, 2026-05-15]
