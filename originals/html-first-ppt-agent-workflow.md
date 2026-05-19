---
type: original
title: HTML-first 的 Agent PPT 工作流
date: '2026-05-18T00:00:00.000Z'
tags:
  - agent
  - html
  - openclaw
  - ppt
  - workflow
---

Henry 提到现在在 X 和 YouTube 上有一个前沿趋势：用 HTML 来替代 PPT，或者用 HTML 来生成 PPT。他想了解这里面的想法和细节。

判断：HTML 更适合 Agent 的创作和演示阶段，因为 HTML/CSS/JS 是模型更擅长生成、预览、调试和迭代的媒介；PPTX 仍然适合作为企业客户的最终可编辑交付格式。

建议路线：HTML-first for creation，PPTX-final for business handoff。先生成 HTML deck 用于快速预览、修改、演讲；需要企业交付时再导出 PDF/PPTX，或使用 PptxGenJS/PPTAgent 生成真正可编辑的 PPTX。

[Source: User, Telegram, 2026-05-18]
