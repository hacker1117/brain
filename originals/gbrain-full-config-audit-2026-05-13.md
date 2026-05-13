---
type: original
title: GBrain 全面配置审计与修复 — 2026-05-13
date: '2026-05-13T00:00:00.000Z'
tags:
  - autopilot
  - configuration
  - cron
  - gbrain
  - openclaw
---

# GBrain 全面配置审计与修复

## Henry 的决策

"我之前是用了一个其他的模型，读了这个内容帮我配置，但我感觉它非常多的地方都配置错误、配置失败了，所以我想请你从头到尾的帮我检查一遍，如果没做的就帮我再做一遍，如果做了的话，也可以用你的方式重新做一遍。总之，我是要确保我在 OpenClaw 里和 gBrain 里的所有的配置，是按照这个文件里的内容来正确的配置好的。"

[Source: User, Telegram, 2026-05-13]

## 参考文档

`https://raw.githubusercontent.com/garrytan/gbrain/master/AGENTS.md` 及其链接的 `INSTALL_FOR_AGENTS.md`、`GBRAIN_VERIFY.md`、`docs/guides/cron-schedule.md`

## 发现的问题清单

### 问题 1：autopilot 连接 PGLite 而非 Supabase（高严重性）

`com.gbrain.autopilot.plist` 使用的是旧的 `autopilot-run.sh`，该脚本中没有注入 `GBRAIN_DATABASE_URL`，导致 autopilot 每次启动都 fallback 到本地 PGLite，无法操作 Supabase 里的真实数据。

**表现**：`[autopilot] running steps inline (engine=pglite)`
**修复**：重写 `~/.gbrain/autopilot-run.sh`，显式 export `GBRAIN_DATABASE_URL`

### 问题 2：Live Sync cron 分支名错误

`GBrain Live Sync` cron job 的 payload 里写的是 `git pull origin master`，但 brain repo (`~/brain`) 用的是 `main` 分支，导致每次 sync 时 git pull 失败。

**修复**：更新 cron payload，改为 `git pull origin main`

### 问题 3：Dream Cycle cron 硬编码 API Key

payload 里直接写了 `export OPENAI_API_KEY='sk-...'`，安全隐患，且维护困难。

**修复**：移除硬编码，改为通过 gbrain CLI 环境变量注入

### 问题 4：OpenClaw MCP 用 stdio 冷启动（导致 query 超时）

详见 `originals/gbrain-query-timeout-root-cause-2026-05-13`

### 问题 5：HTTP server proxy 导致 embedding 失败

`ai.openclaw.gbrain-mcp.plist` 设置了 socks5 代理，bun 不支持，导致 put_page 无法 embed。

**修复**：embedding model 改为 `litellm:text-embedding-3-large`，走 LiteLLM 中转

## 修复后状态

| 项目 | 状态 |
|------|------|
| gbrain version | v0.32.5 |
| Engine | Supabase Postgres |
| Embeddings | 100% (via LiteLLM) |
| Autopilot | Minions worker 模式，连 Supabase |
| OpenClaw MCP | streamable-http :3131 |
| Live Sync | 每 15min，git pull main |
| Dream Cycle | 每天 02:00 |
| Doctor score | 90/100 |

## 关于前一个模型的配置质量

Henry 的判断是准确的：前一个模型在配置时犯了多处错误——autopilot 连错了数据库、cron 分支名写错、MCP transport 没有切换到 HTTP 模式、代理配置干扰了 embedding。这些都是"配置了但实际没工作"的典型问题。

[Source: Livis audit, 2026-05-13]
