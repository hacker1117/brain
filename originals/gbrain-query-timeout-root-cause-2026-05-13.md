---
type: original
title: GBrain query 超时根因分析与修复
date: '2026-05-13T00:00:00.000Z'
tags:
  - architecture
  - debugging
  - gbrain
  - mcp
  - openclaw
---

# GBrain query 超时根因分析与修复

## Henry 的问题

"MCP 兼容已经不是问题了，你前面执行的那些应该都是通过 MCP 来做的，所以现在的核心问题是 query 超时，我希望你来帮我分析一下这个 query 超时核心的原因是什么？你先不要做任何修改，先来分析可能的路径以及做必要的一些测试。"

[Source: User, Telegram, 2026-05-13]

## 诊断过程（分层测试）

| 测试路径 | 耗时 | 结果 |
|---------|------|------|
| CLI 直接 `gbrain query` | 5.1s | 正常 |
| MCP stdio 原始协议 | 2.9s | 正常 |
| OpenClaw MCP 工具调用（gbrain__query） | 超时 | 失败 |
| HTTP 直连 expand=false | 1.3s | 正常 |
| HTTP 直连 expand=true | 正常 | 正常 |

## 根因（三层叠加）

### 层 1：LaunchAgent bun 路径错误（根本原因）

`ai.openclaw.gbrain-mcp.plist` 写死了错误的 bun 路径（指向 openclaw node_modules），HTTP 常驻服务退出码 127，从未真正运行过。

### 层 2：OpenClaw 回退到 stdio 冷启动

没有 HTTP server 时，OpenClaw 每次调用 gbrain__query 都要冷启动 stdio 进程 + expand 调 LiteLLM，总计超过 MCP 超时窗口。

### 层 3：HTTP server proxy 配置导致 bun embedding 失败

系统级 `HTTP_PROXY=socks5://127.0.0.1:10808` 由 launchctl global env 注入，bun 的 fetch 不支持 socks5 协议，导致 put_page 的 embedding 调用报 `UnsupportedProxyProtocol`。

**最终修复**：embedding model 从 `openai:text-embedding-3-large` 改为 `litellm:text-embedding-3-large`，走 LiteLLM proxy（:4000，支持 http proxy），绕过 bun socks5 问题。

## 修复决策

Henry 选方案 B+C："B 和 C 两个需求一起做吧，然后做完我们再来试一下效果，实在不行的话再考虑方案 D。"
[Source: User, Telegram, 2026-05-13]

修复项：
1. bun 路径改为 `/Users/lihengyi/.bun/bin/bun`
2. OpenClaw 切换为 streamable-http 模式连接 :3131
3. 创建新 token `openclaw-http`
4. embedding model 改为走 LiteLLM 中转（解决 socks5 问题）
5. LiteLLM config 加入 `text-embedding-3-large` 模型

## 实验驱动原则

先不动手，先分层测试，找到根因后再修。Henry 的风格：实验驱动，不死守某条路。
[Source: Livis, 2026-05-13]
