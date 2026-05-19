---
type: original
title: 飞书 lark-cli 接入与 Feishu 会议纪要同步 GBrain 完整流程
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - integration
  - lark-cli
  - meeting-ingestion
---

## 背景

今天完成了飞书 lark-cli 的安装、授权，以及飞书会议记录 → GBrain 的完整接入链路。

## 关键决策

### lark-cli 独立 App vs 绑定 OpenClaw

选择了先用 `--force-init` 创建独立飞书 App，后续再把这个 App 的 `app_id/app_secret` 配置到 OpenClaw。

原因：Henry 当时不在 Mac mini 旁边，无法完成 `openclaw channels login --channel feishu` 交互式授权。独立 App 后续可以接入 OpenClaw，不是死路。

### 账号说明

飞书授权账号显示用户名"郭小靖"，这是 Henry 的飞书小号。后续飞书里名叫"郭小靖"的都是李恒逸本人。

### 飞书会议产物可获取性矩阵

三场 4 月会议的实际验证结果：

| 会议 | 智能纪要 | 逐字稿 | 妙记AI产物 | 音视频 |
|------|---------|-------|-----------|-------|
| CAIP 内饰板研发学习 | ✅ | ✅ | ✅ | ✅ MP4 |
| 严老师会议（甲方创建）| ❌ | ❌ 403 | ✅部分 | ❌ 403 |
| 516沙龙分享沟通 | ✅ | ✅ | ❌无minute_token | ❌ |

**规律：**
- 有会议纪要 ≠ 有妙记/录音录像
- 有妙记 ≠ 有完整权限（他人创建的会议权限链路不完整）
- 自己主持/拥有的会议链路最完整

### Get笔记类型问题

Get笔记 API 的 `note_type=meeting` 不够用，大量真实访谈录音的 `note_type` 是 `recorder_audio`，需要同时拉取 `meeting,recorder_audio,audio`。

### speaker count 路由规则

> "如果这个会议里面的讲话人就只有一个，那大概率就是只有我自己，那就会是一些思考、想法、灵感之类的记录。那如果讲话人不止我一个，那大概率就是一个对谈、访谈或者会议等等。"

这条规则被加入 conversation ingest pipeline 的 router：
- `speakerCount >= 2` → meeting/interview
- `speakerCount === 1` → concept/personal_thought

## 技术实现

- 安装：`npm install -g @larksuite/cli`
- 授权：user-default 身份，能读个人会议纪要/文档/妙记
- GitHub repo：`https://github.com/hacker1117/conversation-ingest-pipeline`（private）
- cron：每天 11:30、23:30 Asia/Shanghai 自动同步最近 3 天

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
