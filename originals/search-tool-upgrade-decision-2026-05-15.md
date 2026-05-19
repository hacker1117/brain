---
type: original
title: 搜索工具升级决策：Perplexity + Exa
date: '2026-05-15T00:00:00.000Z'
tags:
  - exa
  - infrastructure
  - openclaw
  - perplexity
  - search
  - tools
---

## 背景

Henry 主动询问当前 OpenClaw 使用的搜索工具及付费选项对比。

## 当前状态

- 当前搜索后端：**DuckDuckGo（免费）**，不稳定

## 付费搜索工具评估

Henry 询问后，推荐优先级：

1. **Perplexity** — 主搜索，付费，增强研究能力
2. **Exa** — 增强研究型搜索，适合做深度调研
3. **Tavily** — 适合自动化 pipeline

## 关于 Google

Henry 特别问了为什么没提 Google。答：OpenClaw 支持的是 **Gemini + Google Search grounding**，不是传统 Google 搜索结果 API；可配，但 Perplexity 更适合做主搜索。

## 决策与行动

- Henry 决定购买 Perplexity API（已购，key 已配置）
- Exa 注册遇到问题：用 Google 账号注册一直报错，暂时搁置
- 后续计划：Exa 问题解决后再接入

[Source: User, Telegram, 2026-05-15]
