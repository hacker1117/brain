---
type: original
title: GBrain MCP Architecture Decision — HTTP 常驻模式
date: '2026-05-12T00:00:00.000Z'
tags:
  - architecture
  - gbrain
  - integration
  - mcp
  - openclaw
---

# GBrain MCP Architecture Decision

## 2026-05-12 初始决策

Henry Lee 和 Livis 在 2026-05-12 下午决定：不使用 stdio 模式，改用 HTTP 常驻服务。

关键发现：
- GBrain MCP Server streamable-http 协议与 OpenClaw 内置 MCP 客户端当时不兼容（后来修复）
- mcporter 连接超时/404
- curl 直接调用 HTTP API 完全正常

当时的决策：CLI fallback，通过 exec() 调用 gbrain CLI 写入数据。

[Source: Livis session, 2026-05-12]

## 2026-05-13 修正

2026-05-13，Henry 确认 MCP 兼容性问题已经解决，正式切换到 HTTP 模式：

1. 修复 `ai.openclaw.gbrain-mcp.plist` 的 bun 路径（从 openclaw node_modules 改为 ~/.bun/bin/bun）
2. 修复 proxy 导致的 embedding 失败（embedding 改走 LiteLLM）
3. OpenClaw config 切换为 `streamable-http → http://localhost:3131/mcp`
4. 创建专用 token `openclaw-http`

**当前架构：**
```
OpenClaw → gbrain MCP token (openclaw-http)
         → streamable-http → http://localhost:3131/mcp
         → gbrain serve --http (LaunchAgent 常驻)
         → Supabase Postgres
```

**embedding 链路：**
```
gbrain HTTP server → LiteLLM proxy (:4000) → OpenAI API
(绕过 bun 不支持 socks5 的问题)
```

这是典型的实验驱动决策：测试了路径，找到根因，换路，不死守。
[Source: Livis, 2026-05-13]
