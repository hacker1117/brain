---
type: concept
title: 飞书接入的双轨收敛策略：独立 App 先行，OpenClaw 后绑
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - feishu
  - gbrain
  - integration
  - lark-cli
  - meeting-ingestion
  - openclaw
---

# 飞书接入的双轨收敛策略：独立 App 先行，OpenClaw 后绑

**来源：** 2026-05-14 OpenClaw session `44a763ea`（transcript hash `a6c06b`）

## 核心洞察

在做飞书接入时，出现了一个顺序依赖问题：

- `lark-cli config init --new` 在 OpenClaw context 下被保护性拒绝，提示应先绑定现有 OpenClaw 飞书 app
- 但 `openclaw channels login --channel feishu` 是交互式命令，Henry 当时不在宿主机旁边

这产生了一个典型的「鸡和蛋」问题——两条路都需要对方先完成。

**突破口：** 独立创建飞书 App，后续再接入 OpenClaw。

> Henry：「那如果我现在就是让它强行单独建一个飞书APP，我后面可以把你建立的这个飞书APP来接入到OpenClaw吗？还是说你强行建立的这个APP后续并不能接入呢？」

Agent 确认：**可以**。用 `--force-init` 创建独立 App，拿到 `app_id/app_secret` 后，可以后续把它配置到 OpenClaw 里，到时候在飞书开放平台补开机器人/事件权限即可。

## 两条线的真实差异

| 维度 | lark-cli 独立 App | OpenClaw Feishu Channel |
|------|-----------------|------------------------|
| 核心用途 | CLI 调飞书 Open API / OAuth / 读取内容 | 机器人能力 / 事件订阅 / 消息接收 |
| 权限范围 | 文档、会议纪要、妙记、日历、任务 | 消息发送/接收、WebSocket/webhook |
| 授权方式 | OAuth 用户授权（user-default）或机器人 token | OpenClaw channel 配置 + 扫码 |
| 是否可互用 | 同一个飞书 App 可以同时服务两条线 | ✅ |

## 决策：lark-cli 官方 CLI 优先

Henry 确认：

> 「好的，那我们就先来做飞书的CLI工具。他们应该自己是开放了对应的CLI工具的，而且好评应该是很多的。所以你先去互联网上搜索一下，确保你拿到的是他们的官方文档。然后你大致地读一遍，再来告诉我我们应该怎么做。」

查到官方 CLI：`@larksuite/cli`（GitHub: `larksuite/cli`），200+ 命令，24 个 AI Agent Skills，覆盖：
- `lark-vc`：搜索会议记录、查询会议纪要（总结、待办、逐字稿）
- `lark-minutes`：妙记元数据与 AI 产物（总结、待办、章节），支持上传音视频生成妙记、下载文件

## Henry 在远程场景下的实用决策模式

Henry 不在宿主机旁边，但仍然推进了 lark-cli 安装和授权，只把必须本人操作的部分（飞书授权页面扫码/点击）交给自己：

> 「你来安装吧。安装好需要做授权的时候，把链接发给我，我来操作。」

这体现了一个实用的人机协作分工原则：**Agent 负责所有可远程执行的部分，人只介入必须人工授权的节点。**

## 目标架构（三层）

```text
lark-cli 读取会议/妙记内容
  → 转成标准 JSON/Markdown
    → GBrain meeting-ingestion
      → 抽取参会人、公司、项目、决策、待办
        → 链接 people/ companies/ projects/
          → 写 timeline
            → 重要问题触发调研
```

同步策略推荐（Agent 建议）：
1. **第一阶段：Cron 拉取**（每 15–60 分钟）——最稳，先跑通全链路
2. **第二阶段：事件订阅/Webhook**——更实时，但权限/回调/内网穿透更复杂

GetSeed 录音可以复用同一套 ingestion pipeline，只是 source adapter 不同。

## 结果

`@larksuite/cli` v1.0.30 已安装，25 个官方 skills 已 symlink 到 OpenClaw，授权完成（见 [[originals/lark-cli-auth-success-2026-05-14]]）。

---
*Related: [[entities/feishu]] · [[entities/getseed]] · [[originals/feishu-getseed-gbrain-integration-plan-2026-05-14]] · [[originals/feishu-openclaw-before-cli-decision-2026-05-14]] · [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]*
