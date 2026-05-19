---
type: concept
title: 飞书会议同步到 GBrain Workflow
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - ingestion
  - lark
  - lark-cli
  - meeting
  - workflow
---

# 飞书会议同步到 GBrain Workflow

## 飞书会议产物可用性矩阵（4月实测）

| 会议 | 会议记录 | 智能纪要 | 逐字稿 | 妙记/录制 | 音视频文件 | 备注 |
|------|----------|----------|--------|----------|-----------|------|
| CAIP 内饰板研发学习 | ✅ | ✅ | ✅ | ✅ | ✅ MP4 | 完整样本，自己主持 |
| 516沙龙分享沟通 | ✅ | ✅ | ✅ | ❌ 404 | ❌ | 有纪要无录制 |
| 严老师会议 | ✅ | ❌ via meeting_id | ❌ 403 | ✅ via minutes search | ❌ 403 | 甲方创建，权限不完整 |

## 关键结论

1. **有会议纪要 ≠ 有妙记/录音录像**（516 就是反例）
2. **有妙记 ≠ 有完整权限**（严老师会议：有 AI 产物，无逐字稿/音视频）
3. **自己主持/拥有的会议链路最完整**（CAIP 是完整样本）

## 推荐同步流程

```text
vc +search（按时间段）
→ vc meeting get（参会人快照）
→ vc +notes（拿智能纪要/逐字稿 doc token）
→ docs +fetch（拉纪要正文）
→ vc +recording（查 minute_token）
→ recording 失败则 minutes +search 兜底
→ vc +notes --minute-tokens（拉 AI 产物：summary/章节/待办）
→ minutes +download（按需下载音视频）
→ 写 GBrain meeting page + availability matrix
```

## lark-cli 设置情况

- `@larksuite/cli` 已安装：版本 1.0.30
- 25 个官方 Lark/飞书 skills 已安装并 symlink 到 OpenClaw
- 授权账号：郭小靖（李恒逸的飞书小号）
- 已有 scope：`minutes:minutes.search:read`、`vc:meeting.search:read`、`vc:note:read`、`vc:record:readonly`、`minutes:minutes:readonly`、`minutes:minutes.artifacts:read`、`minutes:minutes.transcript:export`

## 同步方式建议

- 第一阶段：Cron 拉取（每小时/15分钟）
- 第二阶段：事件订阅/Webhook 实时触发（更复杂，后续加）
- 去重：source + source_id / title + started_at

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
