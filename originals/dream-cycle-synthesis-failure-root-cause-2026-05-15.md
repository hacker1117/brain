---
type: original
title: Dream Cycle Synthesize 失败根因排查与修复记录
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - synthesis
  - unicode
---

## 背景

2026-05-15 GBrain Dream Cycle 多次运行，synthesize 阶段持续失败，导致 transcript 无法处理、brain 无新内容写入。今天完整排查并修复。

## 发现的问题（按层次）

### 第一层：非 BMP emoji / surrogate
- **来源**：Get笔记/录音 transcript 里的说话人标记 emoji（如 `🟢`）
- **路径**：JS → JSON → LiteLLM/Python，emoji 被转成 surrogate pair，低位变成非法 surrogate
- **报错**：`surrogates not allowed`

### 第二层：\u0000 / NUL 字符
- **来源**：OpenClaw transcript 里存在 `GPT\u00004o` 这类字面量，导出后变成真实 NUL 字符
- **路径**：Postgres `jsonb` 不支持 `\u0000`
- **报错**：`unsupported Unicode escape sequence`

### 第三层：工具调用空参数导致循环
- **来源**：Synthesis subagent 调用 `put_page` 时 input 为 `{}`（空参数）
- **后果**：代码 `slug.startsWith(...)` 报 `TypeError: undefined is not an object`，但不立刻终止，错误被喂回模型继续尝试，放大 token 消耗
- **关联**：job 490 单次消耗 679k input tokens 的主因之一

## 修复方案（三层）

1. **数据清理层（三处）**
   - `~/.gbrain/scripts/export-openclaw-sessions.py`：清理 `\u0000`、NUL、非 BMP emoji、surrogate
   - `ingest-pipeline` transcript writer：同样清理
   - `gbrain/src/core/cycle/transcript-discovery.ts`：synthesize 入口兜底 sanitize

2. **工具校验层**
   - `put_page` 入口增加强校验：无 slug / content 非 string → `invalid_params`，不再抛 TypeError
   - `matchesSlugAllowList()` 对缺失/非法 slug 返回 false，不抛 JS runtime error
   - subagent tool dispatcher 增加 JSON-string input parse，防止工具 input 当字符串传递

3. **资源保护层**
   - subagent `max_turns`：30 → 8
   - 单 chunk timeout：30min → 12min
   - wait timeout：35min → 15min
   - 防止坏 transcript / 坏工具调用拖住整个 Dream Cycle

## 验证

- 33 个单元测试全过（`bun test test/operations-allow-list.test.ts test/brain-allowlist.test.ts`）
- `bun run typecheck` 无报错
- dry-run：synthesize 从 fail 变 ok，34 个 transcript 中 26 个会进入合成
- **未触发真实 Dream Cycle 实跑**（等 Henry 确认后再执行）

## 决策

Henry 明确要求：修完后不自动启动 Dream Cycle，先报告问题和修复方案，双方讨论确认无风险后再执行。Dream Cycle cron 已临时暂停，等待手动确认后恢复为每天 02:00 单次。

[Source: User, Telegram, 2026-05-15]
