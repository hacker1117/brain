---
type: original
title: Henry 对 Signal Capture + Dream 架构的核心理解确认
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - dream-cycle
  - gbrain
  - openclaw
  - signal-capture
  - transcript
---

# Henry 对 Signal Capture + Dream 架构的核心理解确认

## Henry 的理解（原话）

> 「好的，我先跟你说一下我的理解，就是说我们首先需要做一个脚本或者project来把OpenClaw里面的session来做成后续需要的transcript。然后后续signal capture这个cron job来读这个transcript来提取有意义的signal，同时dream这个cycle也在凌晨的时候读同样的transcript来做深度的sentence size和patterns的分析，对吗？」

> 「在gBrain的原始设置里面，Dream这个环节就是不读取已经写入的pages，并且也不修改对应的pages的，是吗？」

## 架构要点（确认）

- **共同输入**：Session Transcript Corpus（`/Users/lihengyi/.gbrain/sessions/`）
- **Signal Capture Cron**：快速提取路径，每 45 分钟运行，读 transcript，写入 GBrain originals/entities
- **Dream Cycle**：凌晨深度合成路径，读同一份 transcript，做 patterns 分析、synthesis，生成 dream-cycle-summaries

## 行动输出

Henry 直接下达执行指令：

> 「好的，那你就来写这个exporter吧，就把对应的这个OpenClaw里的session数据，然后做成一个标准的transcript。同时把Dreamcycle里必要的配置也做好。还有signal capture这个Crone job里面需要修改的地方也都修改一遍。」

以及：

> 「你现在来手动跑一遍完整的Dream流程吧。我需要实际的测试一下，我可以接受里面的成本。」

这些操作在同一天已完成（见 [[originals/build-openclaw-session-transcript-exporter-for-gbrain]]）。

[Source: User, Telegram Topics/settings, 2026-05-14]

---
*Related: [[originals/build-openclaw-session-transcript-exporter-for-gbrain]] · [[originals/transcript-corpus-as-shared-input-for-signal-capture-and-dream]]*
