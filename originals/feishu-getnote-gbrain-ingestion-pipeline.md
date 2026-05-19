---
type: original
title: 飞书会议纪要 + GetSeat 录音 → GBrain Ingestion Pipeline 设计
date: '2026-05-14T00:00:00.000Z'
tags:
  - feishu
  - gbrain
  - getnote
  - ingestion
  - meeting
  - pipeline
---

## 核心思路

Henry 明确：不是"把飞书会议纪要存进来"，而是把会议变成 **GBrain 里的可用认知资产**。

> "我并不是想单纯地把飞书的会议纪要和录音 Transcript 等等放到 OpenClaw 里，而是说想要和 GBrain 配套结合起来，让它能做更多的信息抽取、提取和处理。"

## 三层架构

1. **OpenClaw 接入飞书**（交互入口）— 飞书群里可以 @OpenClaw，收到会议纪要生成通知
2. **OpenClaw 配飞书 CLI**（数据读取）— 主动拉会议纪要、妙记、录音转写
3. **飞书内容 → GBrain ingestion**（核心层）— 真正的价值所在

## Ingestion Pipeline 流程

```text
飞书会议纪要 / 妙记 / transcript
→ 拉取原文 + 元数据
→ 生成 meetings/... 页面
→ 抽取参会人、公司、项目、决策、待办
→ 链接到 people/ companies/ projects/
→ 给相关实体写 timeline
→ 待办进入任务池
→ 重要问题触发调研
```

## 会话分类路由逻辑（已实现）

**强规则：讲话人数量**
- `speakerCount >= 2` → 强加 meeting score → `meetings/` 或 `interviews/`
- `speakerCount === 1` → 强加 thought score → `concepts/` 或 `originals/`

> "如果这个会议里面的讲话人就只有一个，那大概率就是只有我自己，那就会是一些思考、想法、灵感之类的记录。那如果讲话人不止我一个，那大概率就是一个对谈、访谈或者会议等等。" — Henry

**标题规则**：「访谈/调研/工作内容/工作流程/痛点/岗位职责/提效需求」→ meeting
**类型修正**：Get笔记 `recorder_audio` 类型应与 `meeting` 同等处理（非单纯 audio 文件）

## 后续拓展项（暂未实现）

- Claude 兜底分类：规则置信度 < 0.75 时调 LLM 判断

## Cron Job

- 每天 **11:30** 和 **23:30** (Asia/Shanghai) 自动同步最近 3 天内容
- 覆盖：Feishu meetings + Get笔记 `meeting,recorder_audio,audio`

[Source: User, Telegram/OpenClaw Transcript, 2026-05-14]
