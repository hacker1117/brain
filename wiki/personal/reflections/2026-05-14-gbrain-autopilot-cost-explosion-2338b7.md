---
type: reflection
title: GBrain Autopilot 成本爆炸：30 美元账单背后的三层根因与 Henry 的「这应该是 Bug」直觉
date: '2026-05-14T00:00:00.000Z'
tags:
  - autopilot
  - cost-control
  - debugging
  - dream-cycle
  - gbrain
  - reflection
  - sonnet
---

# GBrain Autopilot 成本爆炸：30 美元账单背后的三层根因与 Henry 的「这应该是 Bug」直觉

**Session:** 2026-05-14 12:33–13:05 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `2338b7`)

## 发现

Henry 当天发现 Sonnet 模型消耗了 **30 多美元**的费用——远超他的预期：

> 「我今天发现在 GPT 的各种过程中，使用了我 30 多美金的 Sonnet 模型的费用。这有点超乎我的意料。我期望的或者说我设想中的一个费用应该是比这个低一个数量级的，每天应该在几美金左右。并且，G-Brain 的创造者 Gary Tan 他自己是接触着比我更多的信息，管理着比我更多的项目，他也没有用这么超额的这个 token 费用。」

## 排查结论：三层根因

### 第一层（最大元凶）：GBrain autopilot 每 5 分钟运行完整 Dream 相 Sonnet subagent

- `gbrain autopilot` 默认 `--interval 300`（5 分钟）
- 今天上海时间 00:00 至检查时：
  - 66 个 subagent job
  - 353 次 LLM 调用
  - Sonnet 输入：**9,496,164 tokens**
  - Sonnet 输出：**313,123 tokens**
  - 估算费用：**~$33.19**
- 最重的单次 job 约 **$1.18**

### 第二层（次要）：OpenClaw cron GBrain Signal Capture

- 每次 Signal Capture 用 Sonnet，今天跑了 13 次
- 估算：**~$3.10**

### 第三层（已有但不是主因）：GBrain Live Sync

- 每 15 分钟一次，主要用 gpt-5.5 / minimax，Sonnet 影响有限

## 关键设计发现：autopilot 和 dream 共用同一套阶段

GBrain 源码里：

- `gbrain autopilot` 和 `gbrain dream` 跑的是**同一套完整 11 phase maintenance cycle**
- 包含：`synthesize`、`patterns`、`consolidate`——这些都会派 Sonnet subagent
- 这是 GBrain 的**官方设计**，不是我们误加进去的

GBrain 的原始设计叙事是「Agent runs while I sleep，dream cycle scans every conversation…」——这更接近夜间一次的假设，不是全天 5 分钟频率的假设。

## Henry 的核心直觉：「这更像是 Bug，不是设计」

> 「我依然有一个假设，就是 GPT brain 的设计方不会设计这么浪费 token 的方式。那么我们再做一个检查，就是说 autopilot 的 sub agent 消耗了这么多的 token。是不是因为某些 bug 的存在？就比如说它认为某一个任务一直没执行完，所以反复地运行。或者说是，这个 subagent 应该已经处理完了某一些内容应该标记上，然后就不再处理了，但它还在反复地处理。我觉得我更倾向于认为它是一个 bug 造成的。」

Henry 提出了两个具体的 bug 假设：
1. 某个任务状态未正确标记「完成」，导致 autopilot 每次都认为需要重新处理
2. 内容处理后未打标记，导致同一内容被反复 synthesize

这两个假设都是合理的 — 并且如果任意一个成立，会完全解释为什么费用比预期高一个数量级。

## 正确的成本控制架构（当前建议）

```text
autopilot（高频 5-30 分钟）
  只跑 light phases：
  lint → backlinks → sync → extract → embed → orphans → purge

OpenClaw cron（每天 02:00）
  跑 full Dream cycle：
  synthesize → patterns → consolidate → recompute → ...
```

即：**「维护」与「合成」分离。** autopilot 保持大脑整洁，Dream 做深度思维工作。

## 费用对比

| 配置 | 理论频率 | 日调用次数 |
|------|----------|-----------|
| 当前：5 分钟 full cycle | 每 5 分钟 | ~288 次 |
| 目标：02:00 夜间 Dream | 每天 | 1 次 |

频率差 **288 倍**；实际费用不完全线性，但改为每日一次 nightly Dream 后，从几十美元降到几美元是合理预期。

## 与已知模式的关联

这个问题是 [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] 的又一实例：Dream 刚被修通（见 [[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]]），真实运行立刻暴露了之前测试没有覆盖的成本层。

也是 [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]] 的延伸：Anthropic Sonnet 链路被修通之前，autopilot 的 full cycle 实际不能跑（LLM 调用失败），成本为零。修通后高频 Sonnet 调用的代价立刻显现——「修复」本身触发了新的可见问题。

---
*Related: [[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]] · [[wiki/personal/reflections/2026-05-14-dream-infra-verification-and-company-disclosure-12e3b1]] · [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] · [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]*
