---
type: reflection
title: 2026 05 14 Getnote Type Filter And Pipeline Launch 185e90
date: '2026-05-14T00:00:00.000Z'
tags:
  - conversation-ingest
  - debugging
  - feishu
  - getnote
  - ingest-pipeline
  - reflection
---

# Get笔记 note_type 不可信：只取 meeting 导致 14 条中只读到 2 条的教训

**Session:** 2026-05-14 08:33–09:16 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `185e90`)

## 问题

Henry 要求导入 5 月以来所有 Get笔记会议记录，但 pipeline 只返回了 2 条：

> 「我的Get笔记里面，至少存在10篇以上的5月份以后的记录。你来核对一下吧，到底是因为什么原因？是因为API限流的原因？还是因为你判断类型的原因？还是权限的原因？为什么你这里只读到了两个笔记呢？」

## 根因：note_type 字段不可靠，不能只筛 `meeting`

Get笔记 API 5 月以来实际返回 14 条：

| note_type | 数量 |
|-----------|------|
| `meeting` | 2 |
| `recorder_audio` | 9 |
| `audio` | 2 |
| `recorder_flash_audio` | 1（无内容，跳过）|

Pipeline 初版只拉 `--types meeting`，所以只读到 2 条。  
但 9 条真实访谈录音的 `note_type` 是 `recorder_audio`，不是 `meeting`。

## 修复

两步修复：

1. **类型扩展**：拉取 `meeting,recorder_audio,audio`

2. **路由增强**：对标题含「访谈 / 调研 / 工作内容 / 工作流程 / 痛点 / 岗位职责 / 提效需求」的 `recorder_audio`，优先路由为 `interview/meeting`

**commit:** `d7580f6 Route Getnote recorder interviews as meetings`

最终导入 13 条，包括：
- 仓库主管工作职责与AI工具应用需求访谈
- 仓管员日常工作流程与对账痛点沟通访谈  
- 安环工作人员岗位职责与AI应用需求访谈
- 设备维修岗位工作内容与AI辅助需求访谈
- 生产领班日常工作内容与耗时痛点访谈
- 生产计划岗位工作内容与痛点访谈
- 切绳分切计划岗位工作内容与AI应用需求访谈
- AI培训前生产岗位工作需求调研访谈
- 企业工作提效需求收集会议-各部门提报数字化AI工具需求

## 教训

**metadata 字段不等于内容的真实类型。**  
Get笔记用 `note_type` 区分内容来源，不是内容性质。`recorder_audio` 既可能是个人思考录音，也可能是多人访谈录音——靠 `note_type` 无法区分。

这和 [[wiki/originals/ideas/2026-05-14-speaker-count-as-routing-signal-2338b7]] 的核心洞察一致：需要从**内容结构**（说话人数量）而非**来源元数据**来判断内容类型。

## Get笔记授权方式

Henry 提示可以用 Get笔记 CLI 工具直接授权：

> 「我之前在其他渠道里用的是Get笔记的CLI工具。你也可以用这个工具让我直接跟你登录一下吧，对应的这些key或者ID你可以自己存下来，放到环境变量。」

授权成功后凭证写入 `~/.openclaw/openclaw.json`，pipeline 的 getnote adapter 直接读取。

## 飞书导入情况（同步记录）

飞书 4 月 10 日至今共 3 条会议成功写入 GBrain：

- `meetings/2026-04-29-caip-内饰板研发学习` — transcript ✅
- `meetings/2026-04-27-严老师会议` — transcript ❌（甲方创建，权限受限）
- `meetings/2026-04-25-516沙龙分享沟通` — transcript ✅

---
*Related: [[wiki/originals/ideas/2026-05-14-speaker-count-as-routing-signal-2338b7]] · [[originals/lark-cli-feishu-integration-setup-2026-05-14]] · [[wiki/originals/ideas/2026-05-14-ingest-pipeline-git-repo-and-dream-cycle-session-design-9890b4]]*
