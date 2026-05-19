---
type: concept
title: Codex 切换触发 JSON Schema 严格校验 Bug：三层根因排查过程
date: '2026-05-14T00:00:00.000Z'
tags:
  - codex
  - debugging
  - gbrain
  - json-schema
  - openai
  - openclaw
  - reflection
---

# Codex 切换触发 JSON Schema 严格校验 Bug：三层根因排查过程

**Session:** 2026-05-14 04:27–04:39 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `a6c06b`)

## 触发背景

Henry 把 OpenClaw 背后的模型从 Claude 切换成 OpenAI Codex（gpt-5.5），立刻收到报错：

> `LLM request rejected: Invalid schema for function 'gbrain__extract_facts': In context=('properties', 'entity_hints'), array schema missing items.`

## 三层根因（从表象到真相）

### 第一层（表象）：`entity_hints` 缺 `items`

`gbrain/src/core/operations.ts` 里 `entity_hints` 参数只声明了：

```ts
entity_hints: { type: 'array', description: '...' }
// 缺 items: { type: 'string' }
```

Claude 容忍缺 `items`，OpenAI/Codex 严格校验 JSON Schema，直接拒绝。

→ 修复 `operations.ts`，加上 `items: { type: 'string' }`。

### 第二层（重启仍报错）：GBrain 有 schema 转换层

修了 `operations.ts`、重启 GBrain，报错依然存在。原因是 `gbrain/src/commands/serve-http.ts` 里把 params 转成 MCP `inputSchema` 时，只展开了四个字段：

```ts
Object.entries(op.params).map(([k, v]) => [k, {
  type: v.type,
  description: v.description,
  ...(v.enum ? { enum: v.enum } : {}),
  ...(v.default !== undefined ? { default: v.default } : {}),
}])
```

**`items` 被这一层悄悄丢掉了。** 无论 `operations.ts` 里写什么，暴露给 OpenClaw 的 schema 里永远没有 `items`。

→ 修复 `serve-http.ts`，加上 `...(v.items ? { items: v.items } : {})`，同时补全 `pages_updated` 参数的 `items`。

### 第三层（还是报错）：OpenClaw Gateway 缓存了旧 schema

GBrain 修好后，OpenClaw Gateway 在启动时从 GBrain 拉一次工具定义并缓存，之后不会自动刷新。需要重启 OpenClaw Gateway：

```bash
openclaw gateway restart
```

重启后，OpenClaw 重新从 GBrain 拉取 schema，`items` 才正确传递给 Codex。

## 完整修复链路

```
operations.ts (加 items)
  → serve-http.ts (schema 转换层也传 items)
    → GBrain 重启
      → openclaw gateway restart
        → Codex 接受 schema ✅
```

## Henry 的反应与模式

Henry 在第一次"修复"之后、问题仍未解决时，直接提出：

> 「你还是没解决刚才那个报错的问题。你看一下，是因为没有找对地方改错了，还是说，比如说改完之后需要重启或者其他操作之类的？」

然后在第二次也没好之后：

> 「我刚刚切换了一下模型，还是同样的报错。你要么现在先别改，先告诉我到底错误在哪，以及改完之后我到底需不需要手动在改什么配置或者手动重启什么？」

**观察到的 Henry 模式**：他在 agent 反复宣称"已修复"但实际未生效时，会主动要求停下来先做诊断再行动——宁可慢一步，也要先把问题边界搞清楚。这和 [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]] 中他对 GBrain capture 可验证性的坚持是同一底层模式：**信任建立在可验证性上，不接受口头承诺。**

## 技术教训

1. **Claude vs OpenAI JSON Schema 严格度**：Claude 对不完整 schema 容忍度高，OpenAI/Codex 严格按 JSON Schema spec 校验。维护 MCP 工具时，schema 需要按最严格标准写。

2. **中间转换层是隐患**：当有多层系统（source → transform → expose）时，修了源头不代表修了输出。每次调试都要验证最终暴露给 LLM 的 schema，而不只是源代码。

3. **有状态组件的缓存**：Gateway / proxy 类组件通常在启动时缓存上游配置。任何上游变更后，需要主动刷新缓存层。

---
*Related: [[wiki/originals/ideas/2026-05-13-gbrain-warehouse-not-frontal-cortex-876191]] · [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]] · [[entities/feishu]]*
