---
type: concept
title: OpenClaw 搜索工具选型
date: '2026-05-15T00:00:00.000Z'
tags:
  - exa
  - google
  - openclaw
  - perplexity
  - search
  - tavily
  - tools
---

## 背景

2026-05-15，Henry 询问当前 OpenClaw 使用的搜索工具及付费选项。当时默认是 DuckDuckGo 免费后端，不稳定。

## 工具排序（Henry 的选择依据）

| 工具 | 定位 | 推荐优先级 |
|------|------|-----------|
| Perplexity | 主搜索，质量高 | ★★★ 首选 |
| Exa | 增强研究，语义搜索 | ★★ 次选 |
| Tavily | 自动化 pipeline | ★ 按需 |
| Google / Gemini | OpenClaw 通过 Gemini + Google Search grounding 支持 | 备选 |

## 决策

Henry 决定先购买 Perplexity API，已配置（key 已设置）。
Exa 注册遇到 Google 账号报错问题，暂未配置。

## 关键区别

- Perplexity：对话式搜索引擎 API，适合主搜索
- Exa：专为 AI 设计的语义搜索，适合深度研究
- Tavily：面向 Agent 的搜索 API，适合自动化工作流
- Google：OpenClaw 走 Gemini + grounding，不是传统 Search API；已支持但不是最优主搜索

[Source: User, Telegram, 2026-05-15]
