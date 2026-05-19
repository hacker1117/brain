---
type: original
title: 飞书接入的两条线：OpenClaw 入口与 CLI 读取能力
date: '2026-05-14T00:00:00.000Z'
tags:
  - cli
  - feishu
  - gbrain
  - integration
  - openclaw
---

Henry 进一步明确飞书接入要拆成两件事：

> “一方面是OpenClaw接入飞书，一方面是让你可以直接用到飞书的CLI工具，我理解是这两件事都要做，对吧？如果是的话，那么OpenClaw接入飞书和让你用飞书的CLI工具，都需不需要去宿主机上去执行特定的命令？还是我直接在当前的这个对话里就都可以帮我设置好呢？”

这是飞书会议纪要接入 GBrain 的关键分层问题：OpenClaw 接入飞书偏消息/交互入口；飞书 CLI 偏本地读取与同步能力。

[Source: User, Telegram, 2026-05-14]
