---
type: project
title: 飞书会议纪要与 Getseed/Get笔记会议留存接入 GBrain
date: '2026-05-14T00:00:00.000Z'
tags:
  - catsee
  - feishu
  - gbrain
  - getnote
  - getseed
  - ingestion
  - meeting-notes
  - transcript
---

# 飞书会议纪要与 Getseed/Get笔记会议留存接入 GBrain

## 背景

Henry 计划把日常沟通/开会流程整合到 GBrain：包括飞书会议纪要，以及 Getseed / Get笔记（Henry 后续也提到 CatSee，可能是同一类笔记/录音来源或口误，需要后续确认）里的录音、transcript、会议纪要留存。[Source: User, Telegram topic Getseed, 2026-05-14]

Henry 的当前安排是：后续会在另一个 channel 里统一推进「飞书的会议纪要留存」和「CatSee / Getseed / Get笔记的笔记会议留存」。[Source: User, Telegram topic Getseed, 2026-05-14]

## 已完成工作

1. **安装 Get笔记 Skill**
   - 已通过 ClawHub 安装 `getnote` skill，版本 `1.8.3`。
   - 安装位置：`~/.openclaw/workspace/skills/getnote`。
   - Skill 覆盖能力：保存、搜索、列表、详情、知识库、标签、OAuth 配置。[Source: Tool validation, OpenClaw, 2026-05-14]

2. **完成 Get笔记授权**
   - Henry 已完成授权。
   - OpenClaw 已写入 Get笔记 API 凭证配置，可读取 Get笔记 API。[Source: User + Tool validation, Telegram/OpenClaw, 2026-05-14]

3. **验证 Get笔记内容可导出**
   - 已读取最近笔记列表，确认存在多种 note_type：`audio`、`meeting`、`recorder_audio`、`recorder_flash_audio`、`plain_text`、`link` 等。
   - 已导出两条样本到 `~/.gbrain/imports/getnote/`：一条 `audio` 录音笔记、一条 `meeting` 会议笔记。
   - 每条均保存两份文件：`.json` 原始 API 结构，以及 `.md` 可读 Markdown 导出。[Source: Tool validation, OpenClaw, 2026-05-14]

4. **发现字段差异**
   - Get笔记 API 文档描述音频转写在 `audio.transcript`。
   - 实际样本中 `audio.transcript` 为空，转写文本位于 `audio.original`。
   - 后续集成必须兼容 `audio.transcript || audio.original`，不能只按文档字段读取。[Source: Tool validation, OpenClaw, 2026-05-14]

## 已定下的核心原则

### 1. 导出能力是第一验证点

Henry 明确：这些内容不能只在 Get笔记里可见，也不能只是 Skill 能查到；必须能导出成可控的文本/Markdown/JSON 文件，才能稳定进入 GBrain。[Source: User, Telegram topic Getseed, 2026-05-14]

### 2. 不把所有 Get笔记内容粗暴丢进 `meetings/`

GBrain 的 filing rule 是按内容主语归档，不按来源或格式归档。Get笔记只是来源，最终目录取决于内容主语：[Source: GBrain filing rules + User discussion, 2026-05-14]

- `meeting` 或明显会议/访谈内容 → `meetings/`
- 录音但本质是 Henry 自己的想法/判断 → `originals/` 或 `concepts/`
- 原始 API JSON、音频元数据、附件信息 → staging/provenance：先放 `~/.gbrain/imports/getnote/`；后续如进入 brain repo，则作为 raw source / sidecar / `sources/` 处理
- 完整 transcript → 同时进入 meeting page 的 `## Transcript`，以及 `~/.gbrain/sessions/` 供 Dream Cycle 深度处理
- 行动项 → 先进入 meeting page 的 `## Action Items`，后续再任务化

### 3. 引入两阶段路由机制

不能只靠 Get笔记的 `note_type` 判断「会议访谈」还是「个人想法」。推荐机制：[Source: User discussion, Telegram topic Getseed, 2026-05-14]

**第一层：规则判定**
- `note_type=meeting` → 默认会议候选
- 标题/标签含「访谈、会议、沟通、调研、需求、对接、复盘、客户、主管、岗位」→ 加会议分
- transcript 中出现多人对话痕迹，如「问/答」、多人说话、第二人称「你们/贵司/我们这边」→ 加会议/访谈分
- 明显第一人称独白，如「我觉得、我的想法、我意识到、我接下来要」→ 加个人想法分
- 有 attendees / duration / speaker diarization / meeting source → 强会议信号

**第二层：Claude 分类，只处理模糊项**
对规则不确定的内容调用 Claude Sonnet，输出固定 JSON：

```json
{
  "route": "meeting | interview | personal_thought | concept | raw_source",
  "confidence": 0.0,
  "reason": "classification rationale",
  "target_slug_prefix": "meetings | originals | concepts | sources",
  "needs_human_review": true
}
```

低置信度内容（如 `<0.75`）先进入 `inbox/getnote-review/`，不自动进入正式目录。

### 4. Get笔记 AI 总结不能原样作为 GBrain meeting page

Get笔记 AI 总结/会议纪要可以作为输入，但 GBrain 的 `meetings/` page 需要标准化，方便后续检索、实体传播、行动项追踪。[Source: User discussion + GBrain meeting-ingestion skill, 2026-05-14]

推荐 canonical meeting page 结构：

```markdown
---
type: meeting
source_type: getnote
source_id: <note_id>
date: YYYY-MM-DD
title: ...
attendees: []
tags: [...]
---

## Context
这场会是什么背景，为什么重要。

## Executive Summary
3-5 条真正有用的总结，不照搬 AI 套话。

## Key Decisions
明确决策；没有就写「未形成明确决策」。

## Action Items
- [ ] owner: action / due date / source quote

## Entity Signals
涉及的人、公司、项目、概念，以及应该传播到哪些页面。

## Discussion Notes
按主题整理。

---

## Transcript
完整转写，尽量保留原文。
```

原则：transcript 是 ground truth；Get笔记 AI 总结是参考；GBrain adapter 再做结构化归档，但不在导入阶段做过度战略分析，深层模式留给 Dream Cycle / synthesis。

## 下一步建议

1. 在新的 channel 里统一推进飞书会议纪要与 CatSee/Getseed/Get笔记会议留存。
2. 先选 1 条飞书会议纪要 + 1 条 Get笔记会议笔记 + 1 条个人录音想法，跑通完整 pipeline。
3. 写一个 `getnote-to-gbrain` adapter：
   - 拉取新增笔记
   - 导出原始 JSON/Markdown
   - 按两阶段路由分类
   - 生成 canonical meeting / original / concept page
   - transcript 进入 `~/.gbrain/sessions/`
   - 用 `note_id` 做幂等去重
4. 再决定是否接 cron 定时同步。
