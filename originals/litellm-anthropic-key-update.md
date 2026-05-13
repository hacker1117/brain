---
type: original
title: Anthropic Key 已通过 LiteLLM 接入
date: '2026-05-12T00:00:00.000Z'
tags:
  - anthropic
  - configuration
  - gbrain
  - litellm
---

# Anthropic Key 已通过 LiteLLM 接入

Henry 确认：Anthropic 的 API Key 已经通过 LiteLLM Proxy 接入，不需要直连 Anthropic 官方 API。

[Source: User, Telegram, 2026-05-12]

## 接入架构

```
GBrain → LiteLLM Proxy (:4000) → OpenRouter → Anthropic Claude 模型
```

LiteLLM 使用 `openai/` 前缀绕过 LiteLLM 对 `anthropic/` 前缀的特殊处理（后者会忽略 `api_base`，直连官方 API）。

详见：`originals/gbrain-litellm-openrouter-architecture-insight`

## 2026-05-13 补充

embedding model 也改为走 LiteLLM（`litellm:text-embedding-3-large`），解决了 bun 不支持 socks5 代理导致的 embedding 失败问题。

[Source: Livis, 2026-05-13]
