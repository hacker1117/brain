---
type: original
title: Brain-Ops 强制查询——before_prompt_build Hook 插件方案（待实现）
date: '2026-05-13T00:00:00.000Z'
tags:
  - brain-ops
  - deferred
  - hook
  - openclaw
  - plugin
---

# Brain-Ops 强制查询插件方案

## 背景

AGENTS.md 里写了"每条消息先 query GBrain 再回复"，但模型不能稳定遵守——执行优先级问题，靠文字规则不可靠。

需要一个机制让 brain-first lookup 在每条消息上**强制、自动**触发，不依赖模型自觉。

[Source: User, Telegram, 2026-05-13]

## 解决方案：OpenClaw 插件，使用 `before_prompt_build` hook

OpenClaw 插件系统提供 `before_prompt_build` hook，在每次 agent turn、模型调用之前触发，可以动态向 system prompt 注入内容。

### 核心逻辑

```typescript
import { definePluginEntry } from "openclaw/plugin-sdk/plugin-entry";

export default definePluginEntry({
  id: "gbrain-brain-ops",
  name: "GBrain Brain-Ops Auto Query",
  register(api) {
    api.on('before_prompt_build', async (event) => {
      // 1. 提取用户消息关键词
      const userMessage = event.prompt || '';
      if (!userMessage || userMessage.length < 5) return;

      // 2. 调用 GBrain HTTP API query
      const res = await fetch('http://localhost:3131/mcp', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json, text/event-stream',
          'Authorization': 'Bearer gbrain_72a9bc034d5084b9d60ec5359697253dd53de456e469bc5d5bbb1220b1f44c4b'
        },
        body: JSON.stringify({
          jsonrpc: '2.0', id: 1,
          method: 'tools/call',
          params: { name: 'query', arguments: { query: userMessage, limit: 3, expand: false } }
        })
      });
      // 3. 解析结果，prependContext
      const results = parseQueryResults(res);
      if (results.length === 0) return;

      return {
        prependContext: `【GBrain Context】\n${results.map(r => `- ${r.slug}: ${r.chunk_text?.slice(0,200)}`).join('\n')}\n`
      };
    }, { priority: 80, timeoutMs: 5000 });
  }
});
```

### 关键参数

- Hook: `before_prompt_build`，priority 80（在大多数其他 hook 之前跑）
- timeoutMs: 5000（5 秒超时，不阻塞回复）
- GBrain endpoint: `http://localhost:3131/mcp`
- Token: `gbrain_72a9bc034d5084b9d60ec5359697253dd53de456e469bc5d5bbb1220b1f44c4b`（openclaw-http）
- expand: false（避免 LiteLLM 调用延迟）

### 安装方式

```bash
# 在插件目录写好后
openclaw plugins install ./gbrain-brain-ops/
# 或打包后
openclaw plugins install @local/gbrain-brain-ops
```

需要在 openclaw.json 里启用：
```json
{
  "plugins": {
    "entries": {
      "gbrain-brain-ops": {
        "enabled": true,
        "hooks": { "allowConversationAccess": true }
      }
    }
  }
}
```

## 为什么选这条路

| 方案 | 可靠性 | 实现难度 |
|------|--------|---------|
| AGENTS.md 规则 | ❌ 不稳定 | 零成本 |
| Cron 30min 兜底 | ✅ 高（已实现） | 已完成 |
| before_prompt_build hook | ✅ 100% 每条必触发 | 需写 TS 插件，约半天 |

## 决定

2026-05-13 Henry 决定先跑现有方案（Cron 兜底），后续再实现此插件。

[Source: User, Telegram, 2026-05-13]
