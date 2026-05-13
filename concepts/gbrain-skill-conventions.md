---
type: concept
title: GBrain Conventions — Quality Rules
date: '2026-05-12T00:00:00.000Z'
tags:
  - backlink
  - citation
  - conventions
  - gbrain
  - quality
  - skill
---

# GBrain Conventions — Quality Rules

**位置：** `~/gbrain/skills/conventions/quality.md`

跨所有 brain 写入技能的质量规范。

## Citations（强制）

每个写入 brain 的事实必须有内联 `[Source: ...]` 引用：

- 用户陈述：`[Source: User, {context}, YYYY-MM-DD]`
- 会议数据：`[Source: Meeting "{title}", YYYY-MM-DD]`
- 邮件：`[Source: email from {name} re: {subject}, YYYY-MM-DD]`
- 网页：`[Source: {publication}, {URL}, YYYY-MM-DD]`
- 综合：`[Source: compiled from {sources}]`

### 来源优先级（从高到低）

1. 用户直接陈述（最高权威）
2. Compiled truth（brain 的综合理解）
3. Timeline entries（原始证据）
4. 外部来源（API 数据、网络搜索）

## Back-Linking（强制）

每次提到有 brain page 的人或公司，必须从该实体页面创建 back-link。

格式：`- **YYYY-MM-DD** | Referenced in [page title](path) -- 上下文`

未建立 back-link 的引用 = broken brain。

## Notability Gate

创建新 brain page 前先问：
- **人**：会再次互动吗？与工作/兴趣相关吗？
- **公司**：与工作/投资/兴趣相关吗？
- **概念**：是可复用的心智模型吗？值得再次引用吗？

有疑虑则不创建。
