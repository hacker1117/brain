---
type: original
title: 飞书 lark-cli 接入 + 会议同步流程建立（2026-05-14）
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - ingest-pipeline
  - lark-cli
  - meetings
  - setup
---

# 飞书 lark-cli 接入 + 会议同步流程建立（2026-05-14）

## 背景

想把飞书会议纪要/妙记/录音自动接入 GBrain，同时探索飞书接入 OpenClaw。

## lark-cli 安装

- 官方包：`@larksuite/cli`（larksuite 官方维护）
- 版本：1.0.30
- 25 个官方 Lark/飞书 skills 同步安装并 symlink 到 OpenClaw
- 命令：`npm install -g @larksuite/cli`

## 授权方式

1. `lark-cli config init --new --force-init`（单独建飞书 App，不依赖 OpenClaw）
2. 浏览器完成开放平台配置
3. `lark-cli auth login --recommend`（user-default 策略，才能读个人会议/文档）
4. 后续补充 `minutes:minutes:readonly` / `minutes:minutes.artifacts:read` 等 scope

**关键判断**：用 `user-default` 而不是 `bot-only`，因为目标是读个人会议纪要/妙记/云文档。

## 飞书账号注记

- 飞书账号用户名「郭小靖」= 李恒逸的小号
- 飞书里出现「郭小靖」的会议/文档，所有者是李恒逸本人

## 4 月三场会议可获取情况

| 会议 | 会议记录 | 智能纪要 | 逐字稿 | 妙记/recording | 音视频 |
|------|----------|----------|--------|----------------|--------|
| CAIP 内饰板研发学习 | ✅ | ✅ | ✅ | ✅ | ✅ MP4 |
| 严老师会议（甲方创建）| ✅ | ❌ | ❌ 403 | ✅（via minutes search）| ❌ 403 |
| 516沙龙分享沟通 | ✅ | ✅ | ✅ | ❌ 404 | ❌ 无 token |

**规律**：
- 有会议纪要 ≠ 有妙记/录音录像
- 有妙记 ≠ 有完整权限（外部甲方创建时权限链路不完整）
- 自己主持/拥有的会议链路最完整

## 推荐同步流程

```text
vc +search
→ vc meeting get 参会人快照
→ vc +notes 拿智能纪要/逐字稿 doc token
→ docs +fetch 拉纪要正文
→ vc +recording 查 minute_token
→ recording 失败则 minutes +search 兜底
→ vc +notes --minute-tokens 拉 AI 产物
→ minutes +download 按需下载音视频
→ 写 GBrain meeting page + availability matrix
```

## 接入状态

- lark-cli 安装完成，授权通过
- live Feishu adapter 已在 `ingest-pipeline` 实现
- 4 月已知会议 dry-run 验证通过
- [[concepts/unified-conversation-artifact-ingestion-architecture]]

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
