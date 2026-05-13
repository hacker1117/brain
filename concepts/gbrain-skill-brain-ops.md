---
type: concept
title: GBrain Skill — Brain-Ops
date: '2026-05-12T00:00:00.000Z'
tags:
  - always-on
  - brain-ops
  - gbrain
  - skill
---

# GBrain Skill — Brain-Ops

**位置：** `~/gbrain/skills/brain-ops/SKILL.md`

Brain-first lookup。在任何 brain 读写查询操作时触发。

## 核心契约

- Brain 优先于任何外部 API 调用
- 每条入站信号触发 READ → ENRICH → WRITE 循环
- 每个写入事实必须有 `[Source: ...]` 引用
- 用户的直接陈述是最高权威数据
- 每次 brain 写入后维护 back-link（Iron Law）

## Iron Law：Back-Linking（强制）

每次提到一个有 brain page 的人或公司，必须从该实体页面创建指向当前页面的 back-link。

格式：`- **YYYY-MM-DD** | Referenced in [page title](path) -- 简短上下文`

## 文件路径

完整 SKILL.md 位于：`~/gbrain/skills/brain-ops/SKILL.md`
