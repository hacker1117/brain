---
type: original
title: 教训：不要用弱模型做智力型任务
date: '2026-05-13T00:00:00.000Z'
tags:
  - claude
  - configuration
  - gbrain
  - lesson
  - minimax
  - model-selection
---

# 教训：不要用弱模型做智力型任务

## Henry 的观察

"我在安装 gBrain 的过程中，前面使用的一个模型叫 Minimax，它读的文章 Readme 之类的东西和你现在用的 Claude 模型是一模一样的，但它很多的设置都做错了，并且很多的错误也都没有发现。我觉得这两天浪费我花很大的时间去排查问题，甚至去分析问题，根本的原因就是这个 Minimax 模型并没有像 Claude 模型一样去做很好的指令遵循。"

[Source: User, Telegram, 2026-05-13]

## 具体错误清单（Minimax 配置的结果）

| 错误 | 影响 |
|------|------|
| autopilot 连接本地 PGLite 而非 Supabase | 所有 autopilot 操作对真实数据库无效 |
| Live Sync cron 分支写成 master（应为 main） | 每次 sync git pull 都失败 |
| LaunchAgent bun 路径写成 openclaw node_modules | HTTP server 退出码 127，从未真正运行 |
| plist 里 socks5 proxy 未清理 | bun 的 fetch 不支持 socks5，所有 embedding 失败 |
| gbrain-skills/ 目录分类错误 | 不符合 brain 架构规范 |
| Signal Detector 格式不规范 | 没有 `[Source: ...]`，没有 tags，embedding 未生效 |
| MCP 未切换到 HTTP 模式 | query 超时 |

所有这些错误在当时都没有被 Minimax 发现和自我纠正。

## 根本原因

指令遵循能力差距。两个模型读了同样的文档，但 Minimax 在"理解文档意图 → 推断正确配置 → 验证执行结果"这条链上大量失败。

## 规则（永久生效）

**需要「理解指令 → 判断 → 执行」的任务，必须用 Claude（Sonnet/Opus）。**

适合弱模型（Minimax、GPT-4o-mini 等）的任务：
- 信息提取（结构化输出）
- 格式转换
- 简单分类
- 不需要判断的批量操作

**不能用弱模型的任务：**
- 配置系统、遵循复杂 SKILL.md
- 架构决策
- 调试问题
- 任何需要"如果 A 不行就换 B"的推理

[Source: User, Telegram, 2026-05-13]
