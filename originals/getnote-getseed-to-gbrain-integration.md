---
type: original
title: Get笔记/Getseed 录音与会议纪要接入 GBrain
date: '2026-05-14T00:00:00.000Z'
tags:
  - gbrain
  - getnote
  - getseed
  - meeting-notes
  - transcript
---

Henry 想在 Telegram topic 里推进今天主线任务的另一部分：把 Get seed / Get笔记 里的录音 transcript 和会议纪要放到 GBrain 里面。他指出之前可能把 Getseed 打错成类似描述，需要根据 Get笔记线索找到对应工具；并要求先安装 Get笔记 skill（https://clawhub.ai/iswalle/getnote），安装后再计划下一步测试和集成。

执行状态：已通过 ClawHub 安装 `getnote` skill（版本 1.8.3），安装位置 `~/.openclaw/workspace/skills/getnote`。当前尚未配置 Get笔记 API 凭证（`GETNOTE_API_KEY` / `GETNOTE_CLIENT_ID` missing），下一步需要先授权，再用 1 条录音 transcript + 1 条会议纪要跑通最小闭环。

[Source: User, Telegram, 2026-05-14]
