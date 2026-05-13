---
type: original
title: GBrain + LiteLLM + OpenRouter 架构决策
date: '2026-05-12T00:00:00.000Z'
tags:
  - anthropic
  - architecture
  - gbrain
  - litellm
  - openrouter
---

# GBrain + LiteLLM + OpenRouter 架构决策

## 问题

GBrain 需要 Anthropic API key 来驱动 expansion（查询扩展）和 chat（对话）模型，但 Henry 只有 OpenRouter API key，没有直接的 Anthropic key。

[Source: User, Telegram, 2026-05-12]

## 关键发现

### LiteLLM 的 anthropic provider 陷阱

当 LiteLLM model name 以 `anthropic/` 开头时，LiteLLM 会直接调用 Anthropic 官方 API，**忽略** `api_base` 设置。

### 解决方案：用 `openai/` provider 前缀

把 model name 设为 `openai/anthropic/claude-haiku-4.5`，LiteLLM 就会：
- 把请求发到 `api_base`（即 OpenRouter）
- 路径为 `/v1/chat/completions`
- model 字段设为 `anthropic/claude-haiku-4.5`
- OpenRouter 正确路由到 Claude 模型

## 最终架构

```
GBrain (expansion_model: litellm:claude-haiku-4.5)
  → LiteLLM Proxy (:4000)
    → api_base: https://openrouter.ai/api/v1
    → model: openai/anthropic/claude-haiku-4.5
      → OpenRouter
        → Anthropic Claude Haiku
```

### 可用模型（通过 OpenRouter）

- `openai/anthropic/claude-haiku-4.5` → Claude Haiku 4.5（expansion 用）
- `openai/anthropic/claude-sonnet-4.6` → Claude Sonnet 4.6（chat 用）
- `openai/anthropic/claude-opus-4.6` → Claude Opus 4.6

## 2026-05-13 补充：embedding 也走 LiteLLM

因 bun 不支持 socks5 代理，embedding 从 `openai:text-embedding-3-large` 改为 `litellm:text-embedding-3-large`，LiteLLM 使用 http proxy（:10808）中转，绕过 bun 的限制。

[Source: Livis, 2026-05-13]
