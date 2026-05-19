---
type: original
title: 搜索工具栈决策 — Perplexity 主搜索 + Exa + Tavily
date: '2026-05-15T00:00:00.000Z'
tags:
  - configuration
  - exa
  - perplexity
  - search
  - tavily
  - tooling
---

Henry 今天询问并决定升级 OpenClaw 搜索工具，从免费 DuckDuckGo 切换到付费方案。

## 决策

- **当前状态**：DuckDuckGo 免费后端，不稳定
- **优先配置**：Perplexity API（已购买 key）
- **后续计划**：加 Exa 做增强研究；加 Tavily 做自动化 pipeline
- **Google**：了解到 OpenClaw 支持 Gemini + Google Search grounding，但仍优先 Perplexity 做主搜索

## Henry 原话

> 那我们在调试期间，最初的那一次 Dreamcycle 跑完了吗？结果怎么样？

（注：上下文中 Henry 决策脉络是：先了解各搜索工具区别 → 决定付费 → 购买 Perplexity key → 询问配置）

> 我的 perplexity 的 key 是：pplx-z…4vlz
> 但是 exa 我注册有点问题，我用我的 google 账号注册一直报错。你先帮我配置基本的吧

## 背景

来自 Telegram 群聊 Topics 「配置搜索工具」thread（2026-05-15 15:38-19:17 GMT+8）

[Source: User, Telegram/OpenClaw Transcript, 2026-05-15]
