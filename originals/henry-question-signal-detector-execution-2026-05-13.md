---
type: original
title: Henry 质疑 Signal Detector 实际执行情况
date: '2026-05-13T00:00:00.000Z'
tags:
  - agent-behavior
  - execution
  - reliability
  - signal-detector
---

# Henry 质疑 Signal Detector 实际执行情况

## Henry 的问题

"你现在是不是会每一条消息都调用 Signal Detector 这个 skill 呢？你会自动的去提取关键信息并操作 gBrain 吗？"

[Source: User, Telegram, 2026-05-13]

## 实情

不，我没有每条消息都自动运行 Signal Detector。规则写在了 AGENTS.md 和 MEMORY.md 里，但实际执行流里没有强制触发机制——我会在"觉得有价值"时才手动触发，这不符合 always-on 的设计要求。

这是同一个问题的再次出现：规则写了，但执行流里没有内化为默认行为。

[Source: Livis self-reflection, 2026-05-13]

## 背景

同一个问题此前已发生三次（见 `originals/agent-rules-compliance-question`）。每次的"修复"都是在 MEMORY.md 里加文字说明，但文字说明不能改变执行流。

## 关键观察：为什么文字规则不够

OpenClaw 的 agent loop 里没有"每条消息强制 spawn signal-detector"的机制。规则是在 system prompt 里的文字，Claude 在处理消息时会优先完成主任务，signal capture 经常被推后或跳过。

真正的解法应该是在 OpenClaw cron 或 heartbeat 层面加一个"每条消息后自动写入 brain"的机制，而不是依赖 agent 的自觉性。

## 与此同时确认的信息

- brain 目录 export + git push 已完成，GitHub 有提交记录 `dcf037d`
- `~/brain/originals/` 现在有完整的 .md 文件，可以用 Obsidian/Finder 直接浏览
- Links 存在 Supabase DB，不在文件里，文件里只有正文内容
