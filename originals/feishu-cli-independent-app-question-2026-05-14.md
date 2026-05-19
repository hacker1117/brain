---
type: original
title: 飞书 CLI 独立创建 App 后是否可接入 OpenClaw
date: '2026-05-14T00:00:00.000Z'
tags:
  - app-integration
  - feishu
  - lark-cli
  - openclaw
---

Henry 在无法接触 Mac mini 宿主机的情况下，询问是否可以先让 lark-cli 强行单独创建飞书 App，并后续把这个 App 接入 OpenClaw：

> “现在有一个问题，就是我现在不在Mac mini这个宿主机旁边，所以我没法操作。那如果我现在就是让它强行单独建一个飞书APP，我后面可以把你建立的这个飞书APP来接入到OpenClaw吗？还是说你强行建立的这个APP后续并不能接入呢？”

判断：理论上可以复用同一个飞书 App 后续接入 OpenClaw，只要拿到 app_id/app_secret 并在飞书开放平台开启 OpenClaw 需要的机器人、事件订阅、权限、连接方式。但要注意 CLI 初始化出来的 App 起初是面向 CLI/OAuth，未必自动具备 OpenClaw bot channel 所需配置。

[Source: User, Telegram, 2026-05-14]
