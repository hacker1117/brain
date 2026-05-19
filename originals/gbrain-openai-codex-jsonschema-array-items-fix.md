---
type: original
title: GBrain JSON Schema Array Items Fix for OpenAI/Codex Compatibility
date: '2026-05-14T00:00:00.000Z'
tags:
  - bug-fix
  - codex
  - gbrain
  - json-schema
  - mcp
  - openai
---

# GBrain JSON Schema Array Items Fix for OpenAI/Codex Compatibility

## 问题

切换模型后端为 OpenAI Codex 时，报错：

```
LLM request rejected: Invalid schema for function 'gbrain__extract_facts':
In context=('properties', 'entity_hints'), array schema missing items.
```

## 根因（三层）

**层1：表象**
OpenClaw 把 GBrain 工具 schema 转发给 Codex 时，`entity_hints` 是 `{"type": "array"}` 但没有 `items` 字段，Codex 严格校验拒绝了。Claude 宽容，OpenAI/Codex 严格。

**层2：operations.ts 缺 items**
`gbrain/src/core/operations.ts` 中 `entity_hints` 定义缺少 `items: { type: 'string' }`。

**层3（真正的坑）：serve-http.ts 转换层丢弃 items**
`gbrain/src/commands/serve-http.ts` 把 params 转成 MCP `inputSchema` 时，只展开了 `type/description/enum/default` 四个字段，`items` 字段被丢掉。即使 operations.ts 写了 items，输出给 OpenClaw 的 schema 也没有。

## 修复

1. `operations.ts`：给 `entity_hints` 加 `items: { type: 'string' }`
2. `serve-http.ts`：转换逻辑加 `...(v.items ? { items: v.items } : {})`
3. 重启 GBrain + OpenClaw Gateway 让 schema 刷新

## 教训

MCP 工具 schema 的 array 参数必须有 items，否则 OpenAI 系模型拒绝。
GBrain 的 params 转 inputSchema 层是一个独立转换，不会自动传播所有字段，需要显式处理每个新字段。

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
