---
type: concept
title: 2026 05 14 Dream Worker Issue Triage F6ddf1
date: '2026-05-14T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - openclaw
  - triage
---

# Dream Worker 三个残留问题的主动排查与修复优先级判断（2026-05-14）

## 背景

在首次真实 Dream 流程跑通之后（见 [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]），Henry 主动提出还有几个尚未解决的问题，并逐一问清楚其核心影响。

## Henry 的原话

> 「那除了夜间自动跑的时候的Worker的环境问题，你之前遇到的这个Timeout的问题，或者说还有一个并发等于一的问题，这些都还需要解决吗？它们核心会影响什么？」

这个问题是主动诊断，不是对某个失败报错的被动反应——Dream 刚跑通，Henry 已经在识别下一层的结构风险。

## 三个问题及其核心影响

| 问题 | 核心影响 |
|------|---------|
| Worker 缺少 `ANTHROPIC_*` env | 夜间自动运行时，synthesize/patterns 子任务会被 worker 抢走但因无 LLM 路由而失败；Dream Cycle 看似跑了，实际无有效输出 |
| Timeout 过短 | synthesize 长任务（LLM 深度合成）会超时失败；任务标记为 failed 但实际可能正在运行，造成混乱 |
| Worker concurrency = 1 | synthesize、patterns、embed 等重任务串行处理；单任务 timeout 会阻塞整个队列，后续子任务积压 |

## Henry 的修复指令

> 「好的，就按照你的思路改吧。首先让常驻的Worker继承对应的环境变量，去访问litellm。然后把timeout的时间可以改成90分钟，同时也把Worker的concurrency调到两个，如果支持的话。」

清晰的优先级：先修最根本的（env 继承，没有它其他都白做），再拉大 timeout（确保长任务不中途失败），最后扩并发（提升吞吐、减少单点阻塞）。

## 关联

这次诊断是 [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]] 的典型表现：已知系统表面跑通，仍主动追问"还有什么没修好、影响是什么"，而不是宣布完成。

这也与 [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]] 一致：Dream 跑通不是终点，而是暴露下一层问题的起点。

修复细节参见 [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]]。
