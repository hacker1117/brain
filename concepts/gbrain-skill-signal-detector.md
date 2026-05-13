---
type: concept
title: GBrain Skill — Signal Detector
date: '2026-05-12T00:00:00.000Z'
tags:
  - always-on
  - gbrain
  - signal-detector
  - skill
---

# GBrain Skill — Signal Detector

**位置：** `~/gbrain/skills/signal-detector/SKILL.md`

Always-on ambient signal capture。每条入站消息都要触发，并行 spawn，不阻塞主响应。

## 核心职责

两件事等优先级：
1. **Original thinking**（首要）— 用户的想法、观察、论断、框架。用原话捕获，不意译。
2. **Entity mentions**（次要）— 人、公司、媒体引用的提取

## 执行规则

- 每条消息都要触发，无例外（纯操作消息如"好的"除外）
- 并行 spawn，不等它完成再回复
- 用用户的**原始措辞**，不改写
- 每次写入页面后做 back-link（Iron Law）
- 每个事实都要有 `[Source: ...]` 引用

## 文件路径

完整 SKILL.md 位于：`~/gbrain/skills/signal-detector/SKILL.md`

参见 RESOLVER.md dispatcher：`~/gbrain/skills/RESOLVER.md`
