---
type: original
title: 日程工具路由：默认飞书，未来钉钉不应冲突
date: '2026-05-18T00:00:00.000Z'
tags:
  - skills
  - 日程管理
  - 路由
  - 钉钉
  - 飞书
---

我现在让你帮我建日程的话，你就会自动调用飞书相关的这些skills吗？假设我后面接入了钉钉的cli工具，会有冲突吗？

[Source: User, Telegram, 2026-05-18]

## Assistant judgment

当前约定建议：未特别说明平台时，日程/会议/忙闲/改约/会议室/邀请参会人默认走飞书。未来接入钉钉 CLI 后，不应让飞书和钉钉底层工具平级抢解释权，而应增加上层 `calendar-router / schedule-manager`：先判断用户显式平台、客户默认平台、默认主平台，再调用对应底层 skill。

[Source: Assistant, Telegram, 2026-05-18]
