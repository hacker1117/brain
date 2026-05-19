---
type: original
title: Dream Cycle Synthesis 失败根因与修复过程
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dreamcycle
  - infrastructure
  - synthesis
  - unicode
---

## 背景

2026-05-15 调试 GBrain Dream Cycle 的 synthesize 失败，Henry 主导排查并决策修复策略。

## 根因（按发现顺序）

1. **非 BMP emoji surrogate**：Get笔记录音 transcript 里的说话人标记 emoji（如 `🎙`），经 JS → JSON → LiteLLM/Python 链路转成 surrogate pair，LiteLLM 转发时报 `surrogates not allowed`

2. **`\u0000` / NUL 字符**：OpenClaw/GWC transcript 里含有 `GPT\u00004o` 这样的文本，导出后变成真实 NUL 字符，Postgres jsonb 报 `unsupported Unicode escape sequence`

3. **工具调用空参数循环**：synthesize subagent 调用 `brain_put_page` 时 input 为 `{}`，触发 `TypeError: undefined is not an object`，不直接中止而是把错误喂回模型继续尝试，导致 job 490 这类高 token 消耗（单 job ~$2.91）

## Henry 的决策

> 我希望你把 Synthesis 那个问题彻底解决。然后呢，整体的 Dreamcycle 我也不希望一天执行两次了，我希望只在晚上 2 点执行一次。
>
> 这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。

## 修复措施

- export-openclaw-sessions.py、ingest-pipeline、transcript-discovery.ts 三层加 sanitize（去 surrogate、NUL、非 BMP）
- `put_page` 入口加强校验，无 slug/content 直接返回 `invalid_params`
- Synthesis subagent：`max_turns: 30 → 8`，单 chunk timeout: `30min → 12min`
- Dream Cycle cron 改为每天仅凌晨 02:00 执行一次（暂停等 Henry 确认后再启用）

## 成本教训

今天实际花费 **$30+**，主要来源：Deep Dream synthesize backlog 批处理（修完后一次性处理积压），204 次 LLM call，6M+ tokens。Signal Capture 高频 Sonnet 也是持续成本源。

[Source: User, Telegram, 2026-05-15]
