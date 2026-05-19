---
type: note
title: 2026-05-12 Soul Audit & GBrain Setup Session
date: '2026-05-12'
---

# 2026-05-12 Soul Audit & GBrain Setup Session

## Summary

今天完成了一件大事：从零搭建了完整的 GBrain 系统，并为 Henry 的 AI Agent 完成了 Soul Audit。

## Key Decisions Made

### GBrain 升级
- 从 v0.27.0 升级到 v0.32.5（跨越6个大版本）
- 所有 35 个 skills 全部安装
- 从本地 PGLite 迁移到 Supabase 云端数据库

### GitHub 同步
- 创建了 `github.com/hacker1117/brain` 公开仓库
- ~/brain 目录已与 GitHub 同步
- 后续 Live Sync cron 会自动 push 更新

### Supabase 迁移
- 创建了 `henry-brain` 项目（region: ap-southeast-1）
- PGLite → Supabase 迁移完成（10 pages, 13 chunks, 100% embedding）
- 原始 PGLite 备份保留在 ~/.gbrain/brain.pglite

### Soul Audit 完成
生成了以下文件（均存放在 ~/.openclaw/workspace/）：
- SOUL.md — 身份定位：全能型搭档，直接沟通风格，实验驱动决策
- USER.md — 完整用户画像：OPC，3个active projects，经历背景
- ACCESS_POLICY.md — 当前单人完全访问，多渠道全开放
- HEARTBEAT.md — 早晨 09:00 简报 + 晚 23:00 总结

### Cron 任务（共5个）
| 任务 | 时间 |
|------|------|
| Live Sync | 每 15 分钟 |
| Dream Cycle | 每天 02:00 |
| Morning Briefing | 每天 09:00 |
| Evening Summary | 每天 23:00 |
| Weekly Doctor | 周一 08:00 |

## Brain 当前状态

- **Engine**: Supabase Postgres（云端）
- **Schema**: v51（最新）
- **Pages**: 10（主要是目录结构和 README）
- **Markdown 仓库**: `~/brain/` + GitHub 双保险
- **Skills**: 42/42 通过 conformance

## Integration Roadmap

已记录在 `inbox/integration-roadmap.md`，优先级如下：

### P0 — 高优先级
1. **Email（Gmail）** — 大量垃圾/营销邮件，需发件人白名单策略
2. **X (Twitter)** — gbrain 有原生 recipe

### P1 — 需要调研
3. **Claude/Codex 对话** — 封闭平台，无官方 API，需换思路
4. **飞书/钉钉/企业微信** — 需要公司层面授权
5. **飞书日历** — Henry 的主要日程管理工具
6. **飞书录音豆/GETSUN 录音卡** — 线下会议录音

### 重要澄清
**Telegram 对话 → gbrain 的自动同步管道目前未建立。** 这是下一步需要解决的核心问题。

## 关于 Anthropic Key

- **作用**：默认 chat 模型、query expansion、dream cycle 合成、pattern detection、think pipeline
- **现状**：Henry 只有 OpenAI key，没有 Anthropic key
- **替代方案**：可以通过 LiteLLM Proxy 桥接 OpenRouter，但非必须
- **结论**：不用 Anthropic key 也能正常运行，core 功能不受影响

## 待完成

- [ ] Telegram 对话 → gbrain 自动同步（Signal Detector 自动化）
- [ ] 从今日对话提炼结构化 brain pages（Henry 要求）
- [ ] Integration 逐个实现

<!-- timeline -->

## Timeline
- 2026-05-12 — 完成 Soul Audit、GBrain 升级、GitHub 同步、Supabase 迁移、Cron 配置
