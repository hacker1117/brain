---
type: note
title: Integration Roadmap
---

# Integration Roadmap

Henry Lee 的 Brain Integration 路线图。记录所有计划接入的数据源和方向，按优先级排序。

## API Key 配置

- OpenAI API Key: ✅ 已配置
- Anthropic API Key: ❌ 未配置（见下方分析）
- OpenRouter: ❌ 考虑作为 Anthropic 替代方案

### Anthropic Key 的作用

1. **默认对话模型** — gbrain 默认用 `claude-sonnet-4-6` 作为 chat 模型
2. **Query expansion** — 搜索时用 Haiku 做查询扩展，提升搜索质量
3. **Dream cycle synthesis** — 夜间合成用 Claude 判断对话重要性
4. **Pattern detection** — 跨对话模式识别
5. **Think pipeline** — deep reasoning 命令

### Anthropic 的 OpenRouter 替代方案

gbrain 内置支持的 provider：openai, google, anthropic, ollama, voyage, litellm-proxy, deepseek, groq, together, llama-server, minimax, dashscope, zhipu, azure-openai

**暂不支持 OpenRouter**，但可以通过以下方式桥接：

方案A（推荐）：用 **LiteLLM Proxy** 指向 OpenRouter
- 安装 LiteLLM（`pip install litellm`）
- 配置文件里加 OpenRouter 作为 upstream
- gbrain 配置 `LITELLM_BASE_URL` 指向本地 LiteLLM
- 用 `litellm:anthropic/claude-sonnet-4-6` 模型 ID

方案B：直接用 OpenAI 兼容模式
- OpenRouter 提供 OpenAI-compatible 端点
- 配置 litellm-proxy recipe 指向 OpenRouter 端点
- 需要实验验证兼容性

方案C：向 gbrain 提 Feature Request
- gbrain 官方支持 OpenRouter

**结论**：不用 Anthropic key 也可以正常工作，搜索质量略降（query expansion 跳过）、dream cycle 合成跳过，但 core 功能不受影响。

---

## Integration 想法列表

### P0 — 高价值，优先做

#### 1. X (Twitter)
- **现状**：未接入
- **方向**：抓取 timeline、mentions，自动提取相关人物/公司页面
- **实现**：gbrain recipes/x-to-brain

#### 2. Email（Gmail）
- **现状**：未接入
- **现状问题**：大量垃圾/营销邮件
- **处理方案**：
  - 接入后先做**发件人白名单**——只处理已知联系人或特定域名
  - 用 gbrain 的 notability gate 过滤——非知名联系人自动跳过
  - 或者：先手动标记重要邮件，其他由 Henry 自行判断是否值得喂进来
  - **建议**：先做小范围试点，只灌 Henry 认为重要的几个联系人的邮件
- **实现**：gbrain recipes/email-to-brain

### P1 — AI 对话渠道（重要但需特殊处理）

#### 3. Claude App / Codex App 对话
- **现状**：Henry 的 AI 对话发生在 Telegram（我）、Claude App、Codex App
- **问题**：这些是封闭平台，没有官方 API 直接导出对话
- **可行方案**：
  - Claude：**目前无官方 API**，但可以定期让 Henry 复制粘贴重要对话片段
  - Codex（Cursor）：有 API/导出能力，可以研究
  - **更好的方案**：让 Henry 在这些 App 里的重要发现，通过一句话/一段话的方式同步给我
  - 或者：做为一个待解决的集成挑战，先记录，后续看平台政策变化

#### 4. 企业微信 / 飞书 / 钉钉 的人类对话
- **现状**：Henry 和客户的沟通在这些平台
- **问题**：这些都是企业 IM，数据都在企业服务器上，API 访问受限
- **可行方案（需要调研）**：
  - **飞书**：开放平台有 API，可以接入群消息、单聊，但需要企业管理员授权
  - **企业微信**：有企业微信 API，但全公司数据需要管理员开通
  - **钉钉**：钉钉开放平台有 API，类似飞书
  - **核心问题**：这些都需要**公司层面**的授权，而不是个人层面
  - **建议**：先做飞书（因为飞书日历已经要接入），看飞书开放平台能做什么

### P2 — 日程与会议

#### 5. 飞书日历
- **现状**：Henry 用飞书管理日程
- **方向**：日历事件 → brain 事件，自动关联参会人员
- **实现**：gbrain recipes/calendar-to-brain（原本是 Google Calendar，需要研究飞书版）
- **备注**：可能需要自建适配器，gbrain 原生支持 Google Calendar

#### 6. 飞书录音豆 / GETSUN 录音卡
- **现状**：线下会议用这些设备录音
- **方向**：录音 → 转写 → 自动处理，提取参会人、话题、结论
- **问题**：
  - 飞书录音豆：需要研究飞书开放平台 API
  - GETSUN 录音卡：硬件设备，需要研究数据导出方式
- **可行方案**：
  - 手动导入：定期把录音文件导入 brain
  - 或者：语音笔记摄入（voice-note-ingest skill）可以处理音频文件

### P3 — 低优先级/探索中

#### 7. 飞书文档
- 飞书云文档内容同步

#### 8. 现有 brain 内容迁移
- Obsidian/Notion/Logseq 迁移

---

## 执行顺序建议

1. **Email 试点**（低风险，先做）——飞书日历 + 录音豆（飞书开放平台）
2. **X**（有 gbrain 原生 recipe）
3. **Claude/Codex 对话**（需要 Henry 主动同步或等平台开放）
4. **企业 IM**（需要公司授权，后续跟进）
