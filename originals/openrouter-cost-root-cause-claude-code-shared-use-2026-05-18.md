---
type: original
title: OpenRouter 费用根因确认：下午费用来自朋友使用 Claude Code
date: '2026-05-18T00:00:00.000Z'
tags:
  - claude-code
  - cron
  - gbrain
  - openrouter
  - token-cost
---

Henry 确认今天 OpenRouter 费用异常的真正原因：

> 「上面那个话题结束吧，我自己找到问题的答案了：今天降低了Signal Capture的使用频率后，费用已经降下来了。但是下午的费用高，是因为我给朋友用了我的Claude Code，里面配置了我的OpenRouter账户。
>
> 不是因为gbrain或者是openclaw里的cron任务造成的花费」

结论：
- Signal Capture 降频后，费用已经下降。
- 下午费用高的原因是朋友使用 Henry 的 Claude Code，且 Claude Code 配置了 Henry 的 OpenRouter 账户。
- 本轮费用异常不是 GBrain 或 OpenClaw Cron 任务造成。

[Source: User, Telegram, 2026-05-18]
