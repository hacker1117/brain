---
type: concept
title: GWC培训课程结构规划：上午理论+下午实操的分层设计，以及低学历制造业受众的工具选择难题
date: '2026-05-14T00:00:00.000Z'
source: openclaw-session
session_id: 3393855c-23cd-41f6-b85a-32878c95f7d1
tags:
  - agent-tools
  - ai-training
  - course-design
  - gwc
  - manufacturing
  - reflection
---

# GWC培训课程结构规划：上午理论+下午实操的分层设计，以及低学历制造业受众的工具选择难题

> 来源：2026-05-14 OpenClaw Telegram session（GWC培训项目 Topic #86）
> 客户：[[entities/companies/gwc-suzhou|固丝德夫沃夫钢绳(苏州)有限公司]] — 电梯钢绳生产外资企业
> 培训日期：2026-05-17

---

## 背景与信息核对

Henry 在此次对话开头提到，GWC项目的前期沟通来自「飞书的一个叫严老师会议的一次meeting」，后续又亲自去现场做调研，记录在 Get笔记 5月11号的会议记录里。本次会话的一个需要拆解的不一致点：

> 「Get笔记的误判，因为我当时介绍的时候是在介绍我的过往履历，我过往履历里确实在一家新能源汽车零部件工厂工作过。」

这条信息已被确认为历史履历背景，与GWC本身无关，应在知识库中区分开。

---

## 课程整体结构（Henry确认版）

### 上午：培训部分

**第一段（Henry主讲）：**
- 偏重 AI 的整体认识
- 涉及案例演示或工具演示，但「不会花大力气在演示上」
- 核心材料：通用 AI 大模型科普 PPT（《读懂AI，用好AI》，第一课）
  - PPT定位：「中小企业老板的AI破局指南：从底层逻辑到各行业真实应用场景」
  - 核心概念：LLM能力与局限、Prompt/Context/Token三个关键词
  - 代表产品提及：GPT-4o、Claude 3.5、通义千问、DeepSeek R1

**第二段（[[entities/people/yan-jianqin-helen|严建琴（Helen）]]主讲）：**
- 更多 HR 方向上的 AI 应用案例
- 目的是给大家启发
- 注：Helen 同时也在 [[wiki/personal/reflections/2026-04-25-516-salon-planning-wen-xinglai-a719b3|魔方咨询5月16号培训]] 中与 Henry 合作

### 下午：实操部分

「需要更多的来探讨的」，是本次会话的重点讨论方向。

---

## 受众画像（重要约束）

Henry 在会话中补充了一个关键信息：

> 「GWC这边的运营人员绝大多数是初中高中学历，最好的是大专。」

再加上此前调研中已知的工厂情境（参见 [[wiki/personal/reflections/2026-05-11-gwc-production-manager-ai-needs-interview-044aed|GWC生产主管调研]] 及各岗位访谈）：

- 没有VPN，无法访问境外先进大模型
- 电脑能力普遍较弱
- 工作场景：生产现场、Excel操作、设备维修记录等

这意味着**工具选择必须是国内可访问的**，但Henry明确反对「只教豆包、Kimi、元宝」：

> 「这样他们会觉得我讲的东西好像也没什么特别的，所以还是要有一些更新的工具来做。」

---

## Henry 对工具层级的明确期望

Henry 对「更先进的AI生产力」的定义做了重要澄清：

> 「当我说我在希望展示一些更高级或者说是更先进的 AI 生产力的时候，我指的不是 Deify、FastGPT 等等，我指的是像 OpenClaw、Hermes Agent 之类的。更有自主操作能力和更强大的 Agent。当然在国内并不能用这两个，但是可能有一些替代性的产品，或者说是中国开发的一些类似的产品。」

这是一个尚待调研的开放问题：**在中国国内，有没有可用的、具有自主操作能力的 Agent 类工具，可以作为 OpenClaw/Hermes 风格 Agent 的可见替代物？**

Henry 对 AI 的最终建议也提出了明确要求：

> 「我需要你去广泛的去搜索和调研一下，再来回复我，而不是只是顺着我说。」

---

## 两个计划中的实操 Demo

### Demo 1：Excel输入输出自动化（Agent分析+写代码）

> 「就是我相当于把它处理前的 Excel 和处理后的 Excel。也就是说业务，他们业务当中的输入输出都放给 agent，然后让 agent 直接去分析，去写代码，帮他们把这个输入输出自动化掉。」

与 [[wiki/personal/reflections/2026-05-11-gwc-production-foreman-daily-workflow-interview-6682e9|GWC生产领班岗位访谈]] 中暴露的手工Excel痛点高度吻合（产量表录入、效率核算等）。

### Demo 2：语音录入→结构化→知识库对话

> 「他们有人提到说，就是要总结一些数据库。我之前也给过他们一些建议，就是我想用这种 skills 的方式，或者你推荐一些更简便的方式，就是可以通过语音交谈的方式直接来输入内容，最终呢，把这个内容结构化，成一些有意思的呢？标准格式的内容，比如说维修故障的产生原因等等。最终我再把它变成一个知识库，然后还可以和这个知识库对话，去查询历史上的经验。」

这与 [[wiki/originals/ideas/2026-05-11-voice-first-data-capture-as-repair-knowledge-infrastructure-8f14f4|录音即入库]] 原始洞察完全对应——设备维修主管访谈中已经验证了这个需求的真实性。

---

## 核心设计张力

| 张力 | 描述 |
|------|------|
| 先进 vs 可用 | 要展示真正有自主能力的 Agent，但受众无VPN、电脑能力弱 |
| 启发 vs 可复制 | 要让他们觉得「有意思、有高度」，又要让他们能带走并自己用 |
| 工具演示 vs 认知建立 | Henry 明确不想一上来就展示效果，要先建立认知框架 |

---

## 待办/未解决

- [ ] 调研中国国内可用的、具备自主操作能力的 Agent 工具（OpenClaw/Hermes 风格替代品）
- [ ] 确定 Demo 1 用哪个具体的 Excel 输入输出场景（建议从5月11日调研的岗位中选）
- [ ] 确定 Demo 2 的技术路径（Skills方式 vs 更简便方式）
- [ ] 要求参会者提前准备哪些数据/工具

---

## 关联

- 客户主页：[[entities/companies/gwc-suzhou]]
- 合作讲师：[[entities/people/yan-jianqin-helen]]
- 训练设计原则：[[wiki/originals/ideas/2026-05-11-ai-training-design-for-low-digitalization-factories-044aed]]
- 语音知识库原型需求来源：[[wiki/originals/ideas/2026-05-11-voice-first-data-capture-as-repair-knowledge-infrastructure-8f14f4]]
- Excel自动化需求来源：[[wiki/personal/reflections/2026-05-11-gwc-production-foreman-daily-workflow-interview-6682e9]]
- 魔方咨询516沙龙（Henry+Helen合作背景）：[[wiki/personal/reflections/2026-04-25-516-salon-planning-wen-xinglai-a719b3]]
