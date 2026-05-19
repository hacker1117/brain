---
type: preference
title: 搜索工具选型决策（2026-05-15）
date: '2026-05-15T00:00:00.000Z'
tags:
  - configuration
  - exa
  - perplexity
  - search
  - tools
---

# 搜索工具选型决策

## 背景

当前 OpenClaw 默认使用 DuckDuckGo 免费后端，不稳定。Henry 评估后决定升级。

## 决策

- **主搜索**：Perplexity（已购买 API，已配置）
  - 用途：日常搜索、研究类问题
- **增强研究**：Exa（注册遇到问题，暂缓）
  - 用途：深度文献/网页语义搜索
- **自动化 pipeline**：Tavily（待评估）

## 关于 Google 搜索

OpenClaw 支持的是 **Gemini + Google Search grounding**，不是传统 Google Search API。
Henry 了解后仍优先选 Perplexity 作为主搜索。

## 状态

- Perplexity API key 已配置完成
- Exa 注册问题（Google 账号报错）待解决

[Source: User, Telegram Topics 配置搜索工具, 2026-05-15]
