---
type: concept
title: Ingest Pipeline 的两个可移植性决策：Git Repo 管代码，Sessions 双写供 Dream Cycle
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - design-decision
  - dream-cycle
  - gbrain
  - git
  - ingestion-pipeline
  - portability
  - sessions
---

# Ingest Pipeline 的两个可移植性决策：Git Repo 管代码，Sessions 双写供 Dream Cycle

**Session:** 2026-05-14 07:23–07:26 UTC  
**Source:** OpenClaw Telegram session `44a763ea` (transcript hash `9890b4`)

## 背景

在 [[concepts/unified-conversation-artifact-ingestion-architecture]] 设计稳定后，Henry 提出了两个关于「未来可移植性」的问题，引出了两个重要架构决策。

## 决策一：代码必须放进 Git Repo，不能只在 OpenClaw workspace

Henry 的问题：

> 「我能不能把这一套的代码，不止放在OpenClaw的Workspace里，同时也作为一个repo来放到Git里面管理呢？这样之后如果我还有其他的电脑上要装OpenClaw，都可以用我这次写完的这一套代码。」

**结论：应该这么做，而且是正确的设计。**

Repo 路径：`~/.openclaw/workspace/ingest-pipeline/`（自身是独立 Git repo）  
GitHub: `https://github.com/hacker1117/conversation-ingest-pipeline`（private）

### 进 repo 的内容

- `src/adapters/feishu.ts`
- `src/adapters/getnote.ts`
- `src/adapters/local-audio.ts`
- `src/core/*`
- `src/cli.ts`
- `prompts/*`
- `config.example.json`
- `README.md`
- cron 配置模板

### 不进 repo 的内容

- 飞书 token / OAuth credentials
- Get笔记 API key
- 原始 transcript 文件
- MP4 / M4A 媒体文件
- `~/.gbrain/imports/*`
- `~/.gbrain/sessions/*`

### 换机器只需

```bash
git clone <repo>
npm install
cp config.example.json config.json
# 重新授权飞书/Get笔记
```

**原则：代码可迁移，数据不泄露。**

---

## 决策二：Transcript 双写，Session 文件供 Dream Cycle

Henry 的问题：

> 「这个过程中产生的一些transcript是不是也都会放到对应的session目录下？后续会被Dream Cycle调用呢？」

**结论：是，但需要双写，不能只存 imports 目录。**

### 为什么需要双写

Dream Cycle 不会自动扫描任意 `~/.gbrain/imports/` 目录。只有写到 `~/.gbrain/sessions/` 或 GBrain 已索引的 session corpus 目录，transcript 才进入 Dream Cycle 的视野。

所以 transcript 必须双写：

| 目标路径 | 用途 |
|----------|------|
| `~/.gbrain/imports/<source>/<id>/transcript.txt` | 来源证据，raw artifact，provenance |
| `~/.gbrain/sessions/<date>-<source>-<slug>-<id>.md` | Dream Cycle 可读，可被合成 |

### Session 文件格式

```markdown
---
source_type: feishu
source_id: 7632564567113403611
type: transcript
title: 516沙龙分享沟通
date: 2026-04-25
linked_page: meetings/2026-04-25-516-salon-sharing-discussion
participants:
  - 李恒逸
  - Helen Yan
---

# Transcript

原始逐字稿……
```

---

## 整体分层设计（最终架构）

```text
Git repo          → 代码和模板（可迁移）
~/.gbrain/imports → 原始产物（永久保留，来源证据）
~/.gbrain/sessions → transcript session 文件（Dream Cycle 输入）
GBrain DB          → canonical page / chunks / facts / links / timeline
```

这四层互不重叠：代码与数据分离，raw 与 synthesized 分离，session 与 canonical 分离。

---

## 相关连接

[[concepts/unified-conversation-artifact-ingestion-architecture]] — 完整架构设计  
[[projects/conversation-ingest-pipeline-implementation]] — 实现记录  
[[wiki/personal/reflections/2026-05-14-pipeline-render-quality-and-gbrain-canonical-structure-9890b4]] — render 质量要求  
[[originals/feishu-lark-cli-setup-and-meeting-sync-2026-05-14]] — lark-cli 接入过程
