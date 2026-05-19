---
type: concept
title: 统一会议/录音接入 GBrain 的 Adapter + Core Pipeline 设计
date: '2026-05-14T00:00:00.000Z'
tags:
  - adapter
  - audio-ingestion
  - feishu
  - gbrain
  - getseed
  - meeting-ingestion
  - workflow
---

# 统一会议/录音接入 GBrain 的 Adapter + Core Pipeline 设计

Henry 提出不要给每一个 meeting 或日常录音入口都写一套独立流程，而是希望形成一套标准流程，只在不同来源处写不同 Adapter：

> “你推荐的这个流程和咱们在另外一个地方聊到的GetFeed的笔记的存储流程是否从机制上是一致的呢？然后是不是有一些流程是可以共用的，但是另外一些部分写成不同的adapt呢？我想请你整体考虑一下这件事。一方面是我们要符合gBrain的要求和处理标准，方便之后更好地提取和应用。另外一方面，我不想给每一个接入进来的meeting，或者说是日常的一些录音的端口，都有一套自己的流程，还是希望有一套标准的流程，只不过对接不同的Adapter。”

判断：飞书会议纪要、GetSeed/GetFeed 笔记、日常录音、上传音视频等应统一为 `Conversation Artifact Ingestion`：来源 Adapter 负责拉取和标准化产物；共享 Core Pipeline 负责 GBrain 页面、raw artifact、实体/项目/待办/时间线抽取。

[Source: User, Telegram, 2026-05-14]
