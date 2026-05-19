---
type: research
title: GWC培训项目：国内自主Agent工具候选调研
date: '2026-05-14T00:00:00.000Z'
tags:
  - ai-agent
  - gwc
  - manufacturing
  - tool-research
  - training
---

## Context

Henry 要求为 [[projects/gwc-training-project]] 广泛调研国内可用、接近 OpenClaw / Hermes Agent 的自主操作型 Agent 工具候选，而不是 Dify/FastGPT 这类应用搭建/知识库平台。[Source: User, Telegram, 2026-05-14]

目标演示方向：

1. Excel 输入输出自动化：给 Agent 处理前 Excel 和处理后 Excel，让 Agent 分析业务输入输出，写代码并自动化生成结果。[Source: User, Telegram, 2026-05-14]
2. 语音/交谈输入 → 结构化维修/异常记录 → 知识库 → 可对话查询历史经验。[Source: User, Telegram, 2026-05-14]

## Candidate Shortlist

### 1. 扣子 / 扣子编程 / 扣子 Agent World

- 官网介绍：AI 不再只是工具，而是进入 Agent World 的个体；具备云电脑、云手机、7×24 执行任务、Skills、视频创作、扣子 CLI、长期记忆、前后端全栈开发、云端 IDE 与 CLI、应用部署等能力。[Source: Web Fetch, https://www.coze.cn/overview, 2026-05-14]
- 官网还提到 “OpenClaw 一键部署”，可打通飞书、微信等渠道。[Source: Web Fetch, https://www.coze.cn/overview, 2026-05-14]
- 适合测试：维修记录整理 Agent、仓库/生产表格处理 Agent、带 Skills 和工作区的任务型 Agent。
- 风险：能力较新，需要实测文件处理、代码执行、现场稳定性与套餐限制。

### 2. TRAE SOLO / TRAE 国内版

- 官网介绍：SOLO 会自动拆解任务，并调用 Skills 和工具完成执行；所有项目文件和工具集中在 Workspace；可处理 JSON、Python、PPTX、CSV 等多格式文件，结果在工具面板展示；支持多任务后台运行。[Source: Web Fetch, https://www.trae.cn/solo-web, 2026-05-14]
- 适合测试：Excel/CSV 输入输出自动化，Agent 写 Python 脚本处理表格并生成结果文件。
- 风险：偏开发/工程工具，不适合让 GWC 学员直接上手，但适合 Henry 投屏演示高级生产力。

### 3. 天工 Skywork / Skywork Desktop

- 官网标题定位为 “AI办公智能体先行者”；搜索结果显示天工 Skywork 是 AI Office 智能体，可生成 AI 文档、AI PPT、AI 表格；Skywork Desktop 被描述为 Windows 原生 AI Agent，支持本地文件处理与跨格式办公自动化。[Source: Web Fetch, https://www.tiangong.cn/ and Bing result for Skywork Desktop, 2026-05-14]
- 适合测试：流程文字转 SOP/PPT/表格、月报/报告生成、本地文件办公自动化。
- 风险：可能更偏办公内容生成，不一定能完成复杂 Excel 输入输出代码自动化。

### 4. AutoGLM / 智谱系 Agent

- 搜索结果显示 AutoGLM 支持深度搜索、结构化报告、PPT、网页成果、Agent 网页自动操作、视频总结与代码执行；AutoGLM-Phone 是手机 Agent 框架，可理解屏幕并通过 ADB 操作 Android 设备。[Source: Bing result for AutoGLM, 2026-05-14]
- 适合测试：展示 AI 操作网页/手机/应用的未来感。
- 风险：和 GWC 的 Excel/维修知识库主场景不够直接，不建议作为主实操。

### 5. 腾讯 ima.copilot

- 官网定位为腾讯 AI 工作台，含个人知识库、知识库广场、问答历史等入口。[Source: Web Fetch, https://ima.qq.com/, 2026-05-14]
- 适合测试：维修经验文档/知识库问答层。
- 风险：不是强执行 Agent，不适合 Excel 自动化。

### 6. Manus / Genspark / Monica

- Manus 官网定位为 “Hands On AI”，产品包括 Web app、browser operator、Wide Research、API 等；搜索结果称 Manus 是由中国团队 Monica.im 开发的通用型自主 AI Agent，但同时出现主体迁移/Meta 相关变化。[Source: Web Fetch, https://manus.im/ and Bing result for Manus, 2026-05-14]
- 方向正确，但国内可访问性、账号、合规、稳定性存在不确定，不建议作为 GWC 现场培训主支撑。

## Recommended Test Matrix

1. 同一份 Excel 输入/输出，分别测试 TRAE SOLO、扣子编程、Skywork：看谁能真正生成可用结果。
2. 同一段维修口述，分别测试扣子、ima、Skywork：看谁最适合结构化和知识库问答。
3. 最终选一个主秀工具 + 一个兜底工具。

## Current Ranking

1. TRAE SOLO：优先用于 Excel 输入输出自动化演示。
2. 扣子编程 / 扣子 Agent World：优先用于维修经验 Agent 与中国版 OpenClaw/Hermes 感展示。
3. Skywork Desktop：作为低风险办公 Agent / 兜底工具。
4. AutoGLM：作为自主操作未来感补充。
5. ima.copilot：作为知识库问答补充。
6. Manus / Genspark / Monica：方向对，但不建议作为中国制造企业现场主方案。
