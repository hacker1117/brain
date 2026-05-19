---
type: original
title: GBrain Signal Capture 与 Dream Pipeline 的断点
date: '2026-05-14T00:00:00.000Z'
tags:
  - dream
  - gbrain
  - openclaw
  - pipeline
  - signal-capture
---

Henry 指出当前 GBrain / OpenClaw 流程里一个关键困惑：30 分钟运行一次的 Signal Capture/Crashdump 似乎应该把日常对话输出给 Dream 或 Context 阶段使用，但实际看起来没有形成闭环。

## Henry's exact words

> 我现在感觉还是搞不太清楚整体的流程。就比如说，我现在OpenClaw里面日常的对话应该是有一个Crashdump，在每隔30分钟运行一次的。这个运行的输入是什么？输出是什么？你输出又输出到哪个文件里？这些输出的东西最后会在Dream阶段或者Context阶段被用到吗？我的理解应该是的，但是看起来好像没有。

## Takeaway

当前实际链路存在断点：Signal Capture 会从 OpenClaw session JSONL 中抽取少量 signal 并写入 GBrain DB，但不会生成 Dream synthesize 所需的 transcript corpus 文件；Dream 读取的是 `dream.synthesize.session_corpus_dir` 下的 `.txt/.md` 文件，因此如果 transcript exporter 没接上，Dream 不会处理日常完整对话。

[Source: User, Telegram, 2026-05-14]
