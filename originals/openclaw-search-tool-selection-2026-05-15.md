---
type: original
title: OpenClaw 搜索工具选型决策
date: '2026-05-15T00:00:00.000Z'
tags:
  - decision
  - exa
  - openClaw
  - perplexity
  - search
  - tavily
  - tools
---

# OpenClaw 搜索工具选型决策

Henry 今天明确了搜索工具升级计划，从免费 DuckDuckGo 后端迁移到付费工具。

## Henry 的原话/决策

「好的，我大概了解了，等会我就去买。」

「我现在想跟你聊一聊你用的搜索工具的事情。你现在用的搜索是什么工具呢？如果我能接受付费的话。就你掌握的知识，或者你广泛的搜索一下之后，告诉我一下现在你支持什么样的搜索工具，它们的区别是什么？我能用到的最好的又是什么呢？」

## 决策结论

- 当前默认：DuckDuckGo（免费，不稳定）
- **优先配置：Perplexity**（主力搜索，付费）
- **增强研究：Exa**（但注册遇到 Google 账号报错问题，暂缓）
- **自动化 pipeline：Tavily**（未来再上）
- Google：有 Gemini + Google Search grounding 方案，但优先级低于 Perplexity

## 执行进展

- Henry 已购买 Perplexity API key（`pplx-z…4vlz`，已配置）
- Exa 注册遇到 Google 账号报错，未完成

## 背景

Henry 对 Google Search API 有疑问：「由于某种原因导致其实 Google 并不好用？」
判断：OpenClaw 走的是 Gemini + grounding，不是传统 Search API；Perplexity 仍是主力推荐。

[Source: User, Telegram Group (配置搜索工具 topic), OpenClaw Transcript, 2026-05-15]
