---
type: reflection
title: Dream 基础设施修复验证方案 & 公司信息披露
date: '2026-05-14T00:00:00.000Z'
topic: settings
source: openclaw-session-bb6d7d72
tags:
  - company
  - dream-cycle
  - gbrain
  - infrastructure
  - openclaw
  - verification
---

# Dream 基础设施修复验证方案 & 公司信息披露

**Session:** 2026-05-14, OpenClaw Telegram topic `settings` (topic id: 14, group `telegram:-1003929811276`)  
**Transcript hash:** 12e3b1  
**Related infra work:** [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]]

---

## 验证方案：不靠手工，要靠脚本

在 Agent 完成一批基础设施修复（Worker env 注入 LiteLLM/Anthropic、timeout 改 90 分钟、worker concurrency 调到 2）之后，Henry 的第一反应是：

> 「如果你已经改完了的话，我现在有什么办法来验证吗？不是通过手工的方式验证，而是来验证你当前的这个修复确实好用。」
> 
> 「你先给我验证的方案哈，先不需要实际进行。」

这是一个典型的"先规划再行动"信号：Henry 不满足于口头确认，也不愿意靠一次手工的 dream 跑来验证——他要的是一个**可复现、可自动运行的测试脚本**，能在后续持续验证修复是否有效。

Agent 给出方案后，Henry 直接批准执行：

> 「好，就按你的方案来做吧，你来写这个测试脚本吧。」

这个决策节点值得记录：Henry 对基础设施改动的验收标准不是"跑一次没报错"，而是"有一个可重复运行的自动验证"。这和 [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]] 中的验证习惯一致，也是 [[wiki/personal/patterns/experiment-driven-decision-making]] 的一个具体实例。

---

## 并发与 Timeout 的核心影响

在 Agent 提出修复方向之前，Henry 也问清楚了这些问题的影响范围：

> 「那除了夜间自动跑的时候的Worker的环境问题，你之前遇到的这个Timeout的问题，或者说还有一个并发等于一的问题，这些都还需要解决吗？它们核心会影响什么？」

Agent 确认后，Henry 给出了明确的执行指令：

> 「好的，就按照你的思路改吧。首先让常驻的Worker继承对应的环境变量，去访问litellm。然后把timeout的时间可以改成90分钟，同时也把Worker的concurrency调到两个，如果支持的话。」

修复内容汇总：
- **Worker env 注入**：让 `gbrain jobs work` 常驻进程继承 LiteLLM/Anthropic 路由变量，使 synthesize / patterns 阶段不再因缺少 LLM 凭证而失败
- **Timeout → 90 分钟**：synthesize 是长任务，原默认 timeout 不足
- **Worker concurrency → 2**：原并发 1 导致 synthesize 与 patterns 等任务串行等待

这三个修复合并对应了 [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] 中指出的：首次真实 Dream 跑出三层独立问题，本次是把剩余问题（worker env、timeout、concurrency）补完的收尾动作。

---

## 公司信息首次披露

在同一次 settings 会话中，Henry 向 Agent 提供了公司营业执照文件：

> 「这是我公司的营业执照信息，你可以从这里面读取到我的公司名称、注册地址和可以经营的业务等等。」

公司基本信息（从营业执照）：
- **公司名称**：杭州富阳李维斯智能科技工作室
- **性质**：个体工商户（工作室）
- **注册地**：杭州富阳

这是 Henry 在 GBrain 里第一次主动披露公司身份信息。他的使用意图明确：让 Agent 在后续对话中能直接引用正确的公司名称、注册地址和经营范围。

Henry 的 GBrain identity：见 [[people/henry-lee]]。

---

## 情绪信号

本次 settings 会话中有一个值得注意的节点：Agent 完成修复后没有及时在 Telegram 回复，Henry 连发了两条：

> 「执行的结果怎么样了？」  
> 「你怎么没有回复我？」

这种连续追问说明：在 Henry 主动授权的关键操作（尤其是"我可以接受成本"的情况下）之后，他对结果反馈有明确的时效预期。沉默被解读为异常，而非正常的"在处理中"。

---

*Related: [[originals/gbrain-dream-worker-env-and-infrastructure-fixes-2026-05-14]] · [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]] · [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]] · [[wiki/personal/patterns/experiment-driven-decision-making]] · [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] · [[people/henry-lee]]*
