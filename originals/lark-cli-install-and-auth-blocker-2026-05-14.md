---
type: original
title: lark-cli 已安装，初始化遇到 OpenClaw 上下文保护
date: '2026-05-14T00:00:00.000Z'
tags:
  - authorization
  - feishu
  - gbrain
  - lark-cli
  - openclaw
---

Henry 授权开始安装飞书官方 CLI：

> “好的，你来安装吧。安装好需要做授权的时候，把链接发给我，我来操作。”

执行结果：`@larksuite/cli` 已安装，版本 `1.0.30`；`npx skills add larksuite/cli -y -g` 已安装 25 个 lark skills 并 symlink 到 OpenClaw。

初始化时运行 `lark-cli config init --new` 被 CLI 拒绝，原因是当前处于 OpenClaw context，CLI 防止创建一个 parallel app 并 shadow existing openclaw binding。CLI 提示应优先使用 `lark-cli config bind` 绑定 Agent 现有飞书凭证；如果用户明确想在此 workspace 创建单独应用，才使用 `--force-init`。

下一步需要 Henry 确认身份策略：`bot-only`（更安全，但不能访问个人资源如个人日历/云盘/会议纪要）或 `user-default`（允许以用户身份访问，适合读取个人会议纪要，但权限风险更高）。

[Source: User, Telegram, 2026-05-14]
[Source: lark-cli output, 2026-05-14]
