---
type: original
title: lark-cli 成功读到4月妙记和会议记录，发现 scope 缺口
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - lark-cli
  - meeting-ingestion
  - minutes
  - scope
  - vc
---

# lark-cli 成功读到4月妙记和会议记录，发现 scope 缺口

## 背景

2026-05-14 授权完成后，搜索5月数据为空（Henry 确认5月确实没有妙记/视频会议），改搜4月后命中数据。

## 搜到的记录

**妙记（minutes）：**
- `CAIP 内饰板研发学习` — 所有者：郭小靖，2026-04-29 14:01，时长 1h22m，token: `obcnby87d4f176627xw9297j`
- `严老师会议` — 所有者：严建琴，2026-04-27 20:23，时长 20m34s，token: `obcnar53sy843548753gs99b`

**视频会议记录（vc）：**
- `CAIP 内饰板研发学习`
- `严老师会议`
- `516沙龙分享沟通`

## 发现的 scope 缺口

尝试拉取会议纪要正文内容时，发现缺少以下 3 个 scope：

```
minutes:minutes:readonly
minutes:minutes.artifacts:read
minutes:minutes.transcript:export
```

这 3 个 scope 是读取妙记全文（summary、todos、逐字稿、章节）的必要权限。后续需要补充授权。

## 确认的人员信息

飞书账号"郭小靖"即是李恒逸本人的小号（已写入 [[people/henry-lee]]）。CAIP 内饰板研发学习会议的所有者是郭小靖，即 Henry。

[Source: User, Telegram, 2026-05-14]
[Source: lark-cli minutes +search / vc +search output, 2026-05-14]

---
*Related: [[originals/lark-cli-auth-success-2026-05-14]] · [[entities/feishu]] · [[wiki/originals/ideas/2026-05-14-feishu-integration-two-app-convergence-a6c06b]]*
