---
type: original
title: Agent 为何不遵守 AGENTS.md 规则——Henry 的观察
date: '2026-05-12T00:00:00.000Z'
tags:
  - agent-behavior
  - gbrain
  - openclaw
  - reliability
  - signal-detector
---

# Agent 为何不遵守 AGENTS.md 规则

## Henry 的核心观察

"写在 agents.md 里面的东西其实未必会遵守。这个原因是因为 context 的问题，还是其他工程设计上的问题？"

"三次了。每一次说的都是下次记住，但实际没有执行。规则写在 AGENTS.md，但我的执行流里并没有排在先响应用户之前。"

"这是否意味着写在 agents.md 里面的东西其实未必你会遵守？"

[Source: User, Telegram, 2026-05-12]

## 2026-05-13 补充：Signal Detector 问题的延伸

Henry 在 2026-05-13 提出了同类问题的延伸：

"这些页面也都是我之前通过 gBrain 的 Signal Detector 这个 Skill capture 之后写入的，是不是之前我用的其他的模型压根没有遵循这个 Skill 的指令呢？"

[Source: User, Telegram, 2026-05-13]

## 根因分析

### 为什么规则不被遵守

1. **执行优先级问题**：规则写在 AGENTS.md，但 agent 的执行流里没有在"先响应用户"之前执行 signal-detector。主响应优先，背景任务被推后甚至遗忘。

2. **Signal Detector 技能没有被正确执行**：前一个模型可能读取了 SKILL.md，但在实际写入时：
   - 没有用标准格式（`type: original`、`[Source: ...]` 引用）
   - 没有做 back-link（iron law）
   - 没有正确分类（originals/ vs concepts/ vs people/）
   - 写入的内容没有 embedding（因为 proxy 问题）

3. **"下次记住"失效**：依赖"决心"而不是"流程改变"，是错误的修复方式。

## 正确的修复方式

从 MEMORY.md 里写入：**signal-detector skill fires in parallel on every message. I don't choose when to run it.**

流程改变而非靠决心。并行 spawn，不阻塞主响应。

[Source: Livis self-reflection, 2026-05-13]

## 已采取的行动（2026-05-13）

用正确格式重新写入今天 session 的所有信号，验证 embedding 是否正常工作（通过 LiteLLM 修复了 socks5 proxy 问题之后）。

参见：`originals/gbrain-full-config-audit-2026-05-13`
