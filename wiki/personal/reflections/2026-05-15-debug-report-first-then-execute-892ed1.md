---
type: concept
title: 2026 05 15 Debug Report First Then Execute 892ed1
date: '2026-05-15T00:00:00.000Z'
tags:
  - debugging
  - dream-cycle
  - gbrain
  - protocol
  - trust
  - working-style
---

# 先报告，讨论确认，再执行 — Henry 的调试协议

在 2026-05-15 的 Dream Cycle synthesize 修复过程中，Henry 明确提出了一个他希望坚持的工作协议：

> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

## 背景

这个要求出现在花了 $30+ token 的成本事故之后。修复过程中曾多次触发"真实 Dream Cycle"验证，每次都导致额外成本，且问题还在迭代（surrogate → NUL → Postgres jsonb，层层递进）。

Henry 在此时明确设立了「报告 → 讨论 → 执行」三步协议，而不是让 agent 直接迭代到完成。

## 这个模式代表什么

这是 Henry 在高成本、高风险操作下对 agent 自主权的一次明确边界划定：

- **不是不信任 agent 的技术判断**，而是希望在真实执行之前有一个人工确认点
- 体现了「人在回路」（human-in-the-loop）的决策偏好，尤其在调试期间
- 与其他时候的「好的，就按照你建议的两件事来做」形成对比 — 低风险时充分授权，高风险时收紧审核

## 延伸观察

同一天他也问：

> 「所以现在 synthesize 还有没有问题？」

这是对 agent 进度透明度的高要求 — 他希望随时知道真实状态，而不是等 agent 认为"好了"才告知。

## 相关

- [[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]] — 当天 synthesize 修复的完整技术记录
- [[originals/gbrain-automation-cost-discovery-2026-05-15]] — 触发此协议的成本事故
- [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]] — 前一天的基础设施修复背景
