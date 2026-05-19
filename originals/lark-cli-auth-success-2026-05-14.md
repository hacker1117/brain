---
type: original
title: 飞书 lark-cli 授权成功并完成初步读权限验证
date: '2026-05-14T00:00:00.000Z'
tags:
  - authorization
  - feishu
  - gbrain
  - lark-cli
  - meeting-ingestion
---

Henry 完成 lark-cli 登录授权：

> “我授权完成好了。”

验证结果：`lark-cli auth status` 显示 token valid，identity 为 user，已授权 `minutes:minutes.search:read`、`vc:meeting.search:read`、`vc:note:read`、`vc:record:readonly`、`docs:document.content:read` 等会议/妙记/文档读取相关 scopes。

初步测试：
- `lark-cli minutes +search --participant-ids me --start 2026-05-01 --end 2026-05-14` 返回 ok=true，但 items=[]。
- `lark-cli vc +search --participant-ids <userOpenId> --start 2026-05-01 --end 2026-05-14` 返回 ok=true，但 items=[]。

这说明 CLI 和授权链路已跑通；接下来需要调整检索条件、时间范围或确认会议纪要/妙记归属账号。

[Source: User, Telegram, 2026-05-14]
[Source: lark-cli auth status / minutes search / vc search output, 2026-05-14]
