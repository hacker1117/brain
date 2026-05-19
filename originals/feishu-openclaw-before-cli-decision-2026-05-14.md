---
type: original
title: 飞书接入顺序：先 OpenClaw 还是先 CLI
date: '2026-05-14T00:00:00.000Z'
tags:
  - decision
  - feishu
  - integration
  - lark-cli
  - openclaw
---

Henry 根据 lark-cli 的 OpenClaw context 保护机制，重新判断接入顺序：

> “那是不是这个意思？就是你之前建议的，我们先安装CLI，把CLI各种都配置好和测试好之后，再来接入飞书到OpenClaw中。但是现在的卡点是我们需要先把飞书接到OpenClaw里，才能让你完成测试，对吗？如果是的话，那我们就接下来先配置飞书来接入到OpenClaw中吧。你需要告诉我来怎么操作。”

实际情况：不是技术上必须先接 OpenClaw；`lark-cli config init --new --force-init` 可以创建独立 CLI app。但因为最终目标是 OpenClaw + CLI + GBrain 同一套飞书身份能力，先接 OpenClaw 再用 `lark-cli config bind` 绑定现有 app 是更干净的路径。

[Source: User, Telegram, 2026-05-14]
[Source: lark-cli config init --help, 2026-05-14]
[Source: OpenClaw Feishu docs, 2026-05-14]
