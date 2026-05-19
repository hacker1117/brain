---
type: concept
title: GWC培训复盘：实操优先策略的验证，以及Trae Solo工具选型的教训
date: '2026-05-18T00:00:00.000Z'
source: openclaw-session
session_id: e62dbead-9edc-4625-a7d3-72ae3c803492
tags:
  - ai-training
  - debrief
  - gwc
  - manufacturing
  - reflection
  - tool-selection
  - trae-solo
---

# GWC培训复盘：实操优先策略的验证，以及Trae Solo工具选型的教训

> 来源：2026-05-18 OpenClaw Telegram session（GWC培训项目 Topic #86）  
> 客户：[[entities/gwc|固丝德夫沃夫钢绳(苏州)有限公司]]  
> 培训日期：2026-05-17（全天 9:00–17:00）  
> 本次对话同步了6000元发票存档（发票号：26332000004121430031，开票方：杭州富阳李维斯智能科技工作室）

---

## 受众画像（再确认）

此前在 [[wiki/personal/reflections/2026-05-14-gwc-training-course-structure-planning-878953|课程结构规划]] 阶段已做过估计，实际执行后进一步确认：

- 主要参会人员：工厂一线管理者和骨干员工
- 学历：高中、大专、普通大学本科为主，「相对来讲学历较低」
- 年龄：大多在40–50岁左右，「年龄较大」
- AI接触程度：「对AI接触得并不多」

---

## 培训策略：大幅提高实操比例

基于受众画像，Henry 在全天培训（9:00–17:00）中大幅调高了实操部分的权重，并且实操的设计远比「讲一个案例」更深入：

> 「实操部分并不是简单地讲一个案例，而是从下载一个实用的工具，帮助他们一步一步配置大模型，然后让他们自己想自己的需求来描述，让他们完成之后，他们遇到任何操作上的问题，AI不知道该怎么问的问题，以及说问完之后怎么导出的问题，全部是给他们实操的时间，然后我来具体解答每一个人的疑问，确保每一个人在使用AI的过程中，都用到了一些实际的场景。」

### 整体反馈

> 「这个整体反馈是还不错的，每一个人都觉得自己用上了很好用的AI工具，并且真正的，哪怕在很小的维度上，解决了自己的问题。」

这印证了 [[wiki/originals/ideas/2026-05-11-ai-training-design-for-low-digitalization-factories-044aed|低数字化工厂AI培训设计原则]] 中的核心判断：不是「AI能做什么」的功能展示，而是「你每天最烦的哪件事，今天就能让AI帮你做」——给方向、给信心、给最小可行路径。

---

## 工具选型复盘：Trae Solo的问题

### 为什么没有用 Claude Code 或 Manus

Henry 最初理想的工具是 Claude Code 或 Manus（参见此前规划中对「更先进Agent类工具」的讨论），但考虑到：

- 参会者电脑配置较低
- VPN 配置是难题
- 账号配置对普通工厂员工不现实

因此最终选用了字节跳动的 **Trae Solo**（国内可访问、模仿 Claude Desktop App 开发）。

### 实际体验评价

> 「实际这次的感觉是Trae Solo的开发并不完整，虽然它是模仿Claude Desktop App开发的。但整体不管是Skill的配置还是模型的配置还是产出的文件的查看和下载功能，都不是非常好用。」

### 后续关注点

> 「之后在培训中需要格外关注这个工具的进展，看有没有哪个时刻能有一个方便用的、容易交互的，且能在国内可以用的一个Agent类型的工具。」

这是此前在 [[wiki/personal/reflections/2026-05-14-gwc-training-course-structure-planning-878953|课程规划]] 中留下的开放问题（「中国国内有没有可用的具有自主操作能力的Agent类工具？」）在实战中的第一次答案——Trae Solo 是目前最接近的选项，但尚不成熟。

---

## 对后续培训的指导意义

Henry 明确提炼的经验：

> 「要根据这个参与培训人员的学历水平、对AI的熟悉水平，以及他们期望是否能在工作中实际用出来的程度，来决定多大程度上是讲那些前沿趋势、fancy的工具和一些酷炫的案例，还是更大程度上去陪着他们把工具一点一点用起来。」

这是一个关于「理论/实操比例」的动态校准框架，详见 [[wiki/originals/ideas/2026-05-18-training-theory-vs-handson-ratio-calibration-1dd4f1|培训理论与实操比例的受众校准模型]]。

---

## 财务记录

- GWC开票资料（存档）：公司名称：固丝德夫沃夫钢绳（苏州）有限公司 / 开户银行：建行苏州胥口分理处 / 银行账号：32201997548051501285 / 税号：91320500660092764Q
- 本次开票金额：¥6,000.00（价税合计）/ 税前：¥5,940.59 / 税额：¥59.41（1%税率）
- 发票号码：26332000004121430031 / 开票日期：2026-05-18
- 销售方：杭州富阳李维斯智能科技工作室（个体工商户）/ 税号：92330183MAKC3B7N3K
- 项目名称：*现代服务*培训费
- 开票人：李恒逸

---

## 关联

- 客户：[[entities/gwc]]
- 前置规划：[[wiki/personal/reflections/2026-05-14-gwc-training-course-structure-planning-878953]]
- 设计原则来源：[[wiki/originals/ideas/2026-05-11-ai-training-design-for-low-digitalization-factories-044aed]]
- 新提炼模型：[[wiki/originals/ideas/2026-05-18-training-theory-vs-handson-ratio-calibration-1dd4f1]]
- 模式层面：[[wiki/personal/patterns/field-research-before-training-delivery]]
- 信任优先模式：[[wiki/personal/patterns/trust-before-transaction-channel-relationship]]
