---
type: original
title: 决定配置 Perplexity 作为主搜索工具
date: '2026-05-15T00:00:00.000Z'
tags:
  - configuration
  - decision
  - perplexity
  - search
  - tools
---

## 决策

Henry 决定购买 Perplexity API，作为 OpenClaw 的主要搜索工具。Exa 注册遇到问题（用 Google 账号注册一直报错），暂缓。

## 上下文

当前 OpenClaw 使用 DuckDuckGo 免费后端，不稳定。付费选项评估：

- **Perplexity** — 首选，综合搜索质量好
- **Exa** — 适合语义/研究型搜索，但注册问题暂搁置
- **Tavily** — 适合自动化 pipeline
- **Google** — OpenClaw 支持 Gemini + Google Search grounding，但非主推

## 关于 Google 的判断

> 完全好像没有提到 Google。是 Google 没有提供对应的 API 吗？还是我没有装插件？还是说由于某种原因导致其实 Google 并不好用？

结论：Google 有，OpenClaw 支持 Gemini + Google Search grounding；但 Perplexity 仍是主搜索更合适的选择。

[Source: User, Telegram, 2026-05-15]
