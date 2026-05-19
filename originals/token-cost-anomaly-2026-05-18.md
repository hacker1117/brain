---
type: original
title: Token 花费异常：Cron 空转烧钱问题（2026-05-18）
date: '2026-05-18T00:00:00.000Z'
tags:
  - ai-ops
  - cost
  - cron
  - gbrain
  - infrastructure
  - openclaw
---

## Henry 的原始观察

> 过去两天我几乎没怎么跟你交互，但是每天的token花费还是超过了20美金。你查一下具体的花费都在哪里吧，同时需要吧所有需要用到Token的地方都给我列出来（尤其是Cron里的任务）

[Source: User, Telegram, 2026-05-18]

## 排查结论

主因：**两个 Cron 空转烧钱，非真实交互导致。**

### 2026-05-16：$38.70
- `GBrain Signal Capture`：**$20.98** — 每 30 分钟跑一次，用 claude-sonnet-4.6，空窗口也启动完整 agent
- `GBrain Live Sync`：**$16.30** — 每 15 分钟跑一次（一天 96 次），用 gpt-5.5，大多数是 "already up to date"
- 真实人工交互：$0.53

### 2026-05-17：$30.72
- `GBrain Live Sync`：**$17.10**
- `GBrain Signal Capture`：**$12.44**
- 其他：Morning/Evening Briefing、conversation-ingest 合计约 $1.17

## 所有 Token 消耗点

Cron（按成本排序）：
1. `GBrain Live Sync` — 每 15 分钟，gpt-5.5，最大持续成本来源
2. `GBrain Signal Capture` — 每 30 分钟（09:00–00:30），claude-sonnet-4.6
3. `conversation-ingest-pipeline-daily-sync` — 每天 11:30/23:30，gpt-5.5，低
4. `Morning Briefing` — 每天 09:00，gpt-5.5，约 $0.4–0.5/次
5. `Evening Summary` — 每天 23:00，gpt-5.5，约 $0.2–0.4/次
6. `GBrain Weekly Doctor` — 周一 08:00，约 $0.23/次
7. `GBrain Deep Dream Cycle` — **已暂停 (disabled)**

非 Cron：
- 普通对话（gpt-5.5）
- OpenClaw subagent（临时任务）
- GBrain query / embed / extract_facts / think / dream
- 图像/视频生成

## 止血建议（已提出）

1. **`GBrain Live Sync` 改成纯 shell cron，不用 agentTurn** — 本质是文件同步，不需要模型
2. **`Signal Capture` 先 shell 检查是否有新 transcript，有才启动 agent** — 避免空跑
3. `Signal Capture` 降模型至 Haiku / cheap model，复杂信号再升
4. Deep Dream 继续保持 disabled，等幂等保护确认后再开

## Henry 的决策（2026-05-18）

> Dream 还是要每天凌晨2点跑
> Signal Capture 每天的0点和12点跑两次，其他时间不运行
> 然后记录一下这个代办，我们明天需要再来回顾一下今天的花费

[Source: User, Telegram, 2026-05-18]

**已执行：**
- ✅ Dream Cycle 恢复 enabled，每天 02:00 Asia/Shanghai
- ✅ Signal Capture 改为每天 00:00 / 12:00 两次（其他时间不运行）
- ✅ 明天 09:30 已设回顾提醒（复盘 5/18 Token 花费）

**待跟进：**
- [ ] 明天 09:30 复盘 5/18 实际花费，重点看降频后 Signal Capture + Dream 成本对比
- [ ] Live Sync 是否也需要降频或改为纯 shell cron
