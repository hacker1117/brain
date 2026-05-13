---
type: original
title: GBrain AI Pipeline 配置完成记录
date: '2026-05-12T00:00:00.000Z'
tags:
  - gbrain
  - litellm
  - openai
  - openrouter
  - pipeline
---

# GBrain AI Pipeline 配置完成

2026-05-12，完成了 GBrain 完整 AI pipeline 的配置：

- **Embedding**：OpenAI `text-embedding-3-large`（初始），后于 2026-05-13 改为通过 LiteLLM 中转（`litellm:text-embedding-3-large`）
- **Expansion / Query 扩展**：LiteLLM → OpenRouter → Claude Haiku 4.5
- **Chat 模型**：LiteLLM → OpenRouter → Claude Sonnet 4.6

系统无需直接的 Anthropic API key 即可正常运行。

[Source: Livis, 2026-05-12]

## 注意

此页面是对 `originals/gbrain-litellm-openrouter-architecture-insight` 的早期记录，详细架构见该页面。
