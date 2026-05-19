---
type: original
title: GBrain Orphan 修复 — 四层防线方案（讨论版）
date: '2026-05-19T00:00:00.000Z'
related:
  - originals/gbrain-orphan-root-cause-analysis-20260519
tags:
  - architecture
  - backlink
  - dream-cycle
  - gbrain
  - orphan
  - signal-capture
  - workflow
---

## 背景

四个根因对应四层修复方案。见 [[originals/gbrain-orphan-root-cause-analysis-20260519]] 的根因分析。

## 四层防线

### 层 1：入口防漏（put_page post-hook）

**问题**：`put_page` 远程写入时 `auto_links.skipped = remote`

**方案 B（推荐）**：`put_page → extract links for this page → add_link`
- 优点：可控，容易开关
- 不推荐方案 A（改 MCP writer 本身）：每次写入变慢，有 LLM 成本
- 不推荐方案 C（纯定时批处理）：延迟大，问题持续积累

### 层 2：生成层带关系

**问题**：Signal Capture / Dream Cycle 写出的 pages 没有 structured links

**方案**：
- Signal Capture 强制带 `related: [project, people, company]` frontmatter 字段
- Dream Cycle 输出必须写 `Source Pages / Related Pages` section
- 兜底：`post_write_linker` job，每天扫新写页面，按 slug/content 归类到 hub

### 层 3：hub 反向收录

**问题**：hub 页面没有反向 inbound 到 leaf pages

**方案**：
- 高置信度（slug 包含 gwc/perplexity/feishu 等关键词）→ 自动连到对应 hub
- 低置信度 → 进 `inbox/link-review` 队列，人工确认
- 不建立 `orphan-index` 大垃圾桶（会让指标好看但图谱变脏）

### 层 4：wiki 产物治理

**问题**：`wiki/*` 缺少 index / topic hub

**方案**：
- 建三个固定 index：`wiki/index/ideas` / `wiki/index/patterns` / `wiki/index/reflections`
- Dream 每生成一页必须：链到对应 index + 来源 transcript + 至少一个主题 hub
- 成熟 pattern 提升路径：`wiki/personal/patterns/...`（探索层）→ `concepts/...`（canonical）

## 目标

让 orphan 成为真正有意义的信号：
**剩下的 orphan = 确实还没归类**，而不是"流程忘了链接"。

## 当前状态

- 方案讨论完成，未实施
- Batch 1 已执行（40 条确定性回链，172→135 orphan）
- 下一步：先验证 `put_page` 在 0.36.1 下是否仍 remote-skip，再决定是否需要 post-hook

[Source: User, Telegram/OpenClaw Transcript, 2026-05-19]
