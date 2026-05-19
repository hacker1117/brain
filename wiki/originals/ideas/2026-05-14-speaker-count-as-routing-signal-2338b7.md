---
type: idea
title: 讲话人数量是会议/个人录音的最强路由信号：一人即思考，多人即对谈
date: '2026-05-14T00:00:00.000Z'
tags:
  - classification
  - conversation-ingest
  - getnote
  - mental-model
  - routing
  - speaker-count
---

# 讲话人数量是会议/个人录音的最强路由信号：一人即思考，多人即对谈

**Session:** 2026-05-14 09:05–09:10 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `2338b7`)

## 核心洞察

Henry 在讨论 conversation ingest pipeline 的分类路由时，提出了一条比标题关键词更接近内容本质的强规则：

> 「如果这个会议里面的讲话人就只有一个，那大概率就是只有我自己，那就会是一些思考、想法、灵感之类的记录。那如果讲话人不止我一个，那大概率就是一个对谈、访谈或者会议等等，我觉得都可以按照会议处理。你觉得补上这条逻辑怎么样？」

这条规则被加入 [[wiki/originals/ideas/2026-05-14-ingest-pipeline-git-repo-and-dream-cycle-session-design-9890b4]] 所描述的 unified conversation ingest pipeline router 中。

## 为什么讲话人数量比标题更可靠

标题是人写的，可能不规范、可能有歧义：
- 「仓库主管工作内容」——是会议还是笔记？标题不确定
- 「AI 工具应用思考」——是个人想法还是讨论录音？也不确定

讲话人数量是内容结构的客观特征，不依赖主观命名：
- 一个说话人 → 独白，即思考、想法、灵感记录
- 两个及以上说话人 → 对话，即访谈、会议、讨论

## 与 Get笔记 note_type 问题的关联

本次发现的直接背景：Get笔记的 `note_type` 字段不可靠。大量真实访谈录音的类型是 `recorder_audio`，而不是 `meeting`。最初 pipeline 只筛 `note_type=meeting`，导致 14 条中只读到 2 条。

修复分两步：
1. 把 `meeting, recorder_audio, audio` 全部纳入
2. 用 speaker count 做强路由，弥补类型字段不准的缺陷

这个规则的价值超出 Get笔记场景——它适用于任何 source adapter 的录音分类，因为说话人数量可以从 transcript 中机械提取，不依赖 metadata 字段的准确性。

## 实现方式

从 transcript 里匹配说话人标记：

```ts
// Get笔记格式
/🎙\s*说话人\d+/g
// 通用格式
/^(?:说话人|Speaker)\s*\d+/gm
```

结论：
- `speakerCount >= 2` → 强加 meeting score，倾向 `meeting / interview`
- `speakerCount === 1` → 强加 thought score，倾向 `concept / personal_thought`

## 这条规则的认识论地位

这是一条**内容结构先于元数据**的原则：当来源提供的元数据（如 `note_type`）不可信时，从内容本身提取客观特征来分类，比依赖外部标签更可靠。

这与 [[wiki/personal/reflections/2026-05-14-pipeline-render-quality-and-gbrain-canonical-structure-9890b4]] 中「GBrain page 是认知资产，不是文件备份」的原则一脉相承：内容处理应该理解内容，不只是搬运内容。

## Claude 兜底分类的决策

Henry 决定将「Claude 兜底分类」记为后续可扩展项，当前不实现：

> 「Cloud的兜底就记录为一个后续可以考虑的拓展项吧。暂时我们就不实现了。」

理由：speaker count 规则 + 标题强信号规则的组合，已能覆盖绝大多数案例；边界情况的 LLM 分类成本暂时不值得引入。

---
*Related: [[wiki/originals/ideas/2026-05-14-ingest-pipeline-git-repo-and-dream-cycle-session-design-9890b4]] · [[wiki/personal/reflections/2026-05-14-pipeline-render-quality-and-gbrain-canonical-structure-9890b4]] · [[originals/lark-cli-feishu-integration-setup-2026-05-14]]*
