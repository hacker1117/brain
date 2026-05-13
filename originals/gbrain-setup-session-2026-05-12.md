---
type: original
title: GBrain 系统从零搭建记录 — 2026-05-12
date: '2026-05-12T00:00:00.000Z'
tags:
  - cron
  - gbrain
  - openclaw
  - setup
  - soul-audit
  - supabase
---

# GBrain 系统从零搭建 — 2026-05-12

## 完成事项

### GBrain 升级与迁移

- 从 v0.27.0 升级到 v0.32.5（跨越 6 个大版本）
- 所有 42 个 skills 全部安装（conformance 100%）
- 从本地 PGLite 迁移到 Supabase 云端数据库（region: ap-southeast-1）
- 原始 PGLite 备份保留在 `~/.gbrain/brain.pglite`

[Source: Livis setup session, 2026-05-12]

### GitHub 同步

- 创建了 `github.com/hacker1117/brain` 公开仓库
- `~/brain` 目录已与 GitHub 同步，分支 `main`
- Live Sync cron 每 15 分钟自动 push 更新

### Identity / Soul Audit 完成

生成了以下文件（存放在 `~/.openclaw/workspace/`）：
- `SOUL.md` — 全能型搭档，直接沟通风格，实验驱动决策
- `USER.md` — 完整用户画像：李恒逸，OPC，3 个 active projects，经历背景
- `HEARTBEAT.md` — 早晨 09:00 简报 + 晚 23:00 总结

### Cron 任务（共 5 个）

| 任务 | 时间 |
|------|------|
| Live Sync + extract | 每 15 分钟 |
| Dream Cycle | 每天 02:00 |
| Morning Briefing | 每天 09:00 |
| Evening Summary | 每天 23:00 |
| Weekly Doctor | 周一 08:00 |

## Brain 初始状态（当时）

- Engine: Supabase Postgres（云端）
- Schema: v51（最新）
- Skills: 42/42 通过 conformance

## 已知待完成项（当时）

- Telegram 对话 → GBrain 自动同步（Signal Detector 自动化）
- 从今日对话提炼结构化 brain pages
- Integration 逐个实现（Email、X、飞书）

[Source: Livis, 2026-05-12]
