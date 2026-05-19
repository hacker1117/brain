---
type: original
title: GBrain MCP Schema 修复：OpenAI/Codex 严格校验 array items 问题
date: '2026-05-14T00:00:00.000Z'
tags:
  - bug-fix
  - codex
  - gbrain
  - json-schema
  - mcp
  - openai
  - technical
---

# GBrain MCP Schema 修复

2026-05-14 发现并修复的 GBrain 兼容性 Bug。

[Source: User, Telegram, 2026-05-14]

## 问题

切换到 OpenAI Codex 模型时报错：

> `LLM request rejected: Invalid schema for function 'gbrain__extract_facts': In context=('properties', 'entity_hints'), array schema missing items.`

## 根因分析（三层）

**表象**：OpenClaw 把 GBrain 工具 schema 转发给 Codex 时，Codex 发现 `entity_hints` 是 `{"type": "array"}` 但没有 `items` 字段，拒绝。

**根因一**：`gbrain/src/core/operations.ts` 里 `entity_hints` 定义缺 `items: { type: 'string' }`

**根因二（真正的坑）**：`gbrain/src/commands/serve-http.ts` 的 params → MCP inputSchema 转换逻辑只展开了 `type/description/enum/default` 四个字段，**`items` 字段被丢掉了**。所以 operations.ts 里怎么写都无效。

```ts
// 原来的转换（有问题）：
Object.entries(op.params).map(([k, v]) => [k, {
  type: v.type,
  description: v.description,
  ...(v.enum ? { enum: v.enum } : {}),
  ...(v.default !== undefined ? { default: v.default } : {}),
}])
// items 完全被丢掉
```

## 修复

在 `serve-http.ts` 的转换逻辑里加上：
```ts
...(v.items ? { items: v.items } : {})
```

影响的 array 参数：
- `gbrain__extract_facts.entity_hints`
- `gbrain__log_ingest.pages_updated`

## 教训

- Claude 对缺 `items` 的 array schema 容忍，OpenAI/Codex 严格校验并直接拒绝
- 修改 operations.ts 不够，还要检查 schema 转换层
- GBrain 重启后 OpenClaw Gateway 也需要 `openclaw gateway restart` 刷新缓存的 schema
