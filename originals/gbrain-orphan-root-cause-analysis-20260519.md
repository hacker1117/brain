---
type: original
title: GBrain Orphan 根因分析 — 不是知识碎片化，是写入流程断了
date: '2026-05-19T00:00:00.000Z'
tags:
  - backlink
  - dream-cycle
  - gbrain
  - graph
  - orphan
  - signal-capture
  - workflow
---

## Henry 的判断

> "我不是为了指标好看，而是想知道指标背后的原因，到底是就应该有这么多orphan，还是哪些流程出了问题"

这是关键区分：graph 结构是否在真实表达知识关系，而不是让 orphan 数字好看。

## 诊断结论

当前 orphan（升级前 212，升级后 135）不是"知识天然碎片化"，而是 **写入 → 链接 → hub 收录** 这条流程断了，具体四个根因：

### 1. `put_page` 远程写入时 `auto_links.skipped = remote`
通过 MCP 写页面时，自动 link extraction 不运行。页面内容创建了，但没有进入 graph。

### 2. Signal Capture / Dream Cycle 写出的 pages 没有自动补 link
pipeline 层面没有强制要求写入时带 related links / project / entity 关联。

### 3. hub 页面没有反向收录 leaf pages
例如 GWC 的 original 会 outbound 链到 project hub，但 hub 没有 inbound 回链这些 leaf pages，导致 leaf pages 仍算 orphan。

### 4. Dream Cycle 的 `wiki/*` 产物缺少 index / topic hub
wiki/ideas、wiki/patterns、wiki/reflections 没有对应 index，生成的每个页面都是孤立的。

## 验证实验

- 给 `inbox/orphan-backlink-repair` 手动加 1 条 inbound link → orphan count 从 213 → 212
- 说明 `gbrain__add_link` 能直接修复 orphan 状态，机制没坏，只是流程没运行

## Batch 1 结果（2026-05-19）

执行确定性 hub 回链 40 条：
- 搜索工具主题：回链 Perplexity/Exa/Tavily/搜索决策 → `concepts/search-tool-selection`
- GWC 培训项目：回链 GWC 相关 originals → `projects/gwc-training-project`
- 结果：212 → 172，下降 40 个，失败 0

## 修复方案方向（讨论中，未实施）

1. **入口层防漏**：`put_page` 后追加 per-page link extraction post-hook
2. **生成层带关系**：Signal Capture / Dream Cycle 输出必须携带 related links 字段
3. **维护层清理**：daily orphan scanner，高置信度自动连，低置信度进 `inbox/link-review`

[Source: User, Telegram/OpenClaw Transcript, 2026-05-19]
