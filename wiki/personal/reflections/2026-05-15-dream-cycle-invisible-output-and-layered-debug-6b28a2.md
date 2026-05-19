---
type: concept
title: 2026 05 15 Dream Cycle Invisible Output And Layered Debug 6b28a2
date: '2026-05-15T00:00:00.000Z'
source: openclaw-session
session_id: f1697da1-46ec-4cb0-ab22-90d067dc80e6
transcript_hash: 6b28a2
tags:
  - debugging
  - dream-cycle
  - gbrain
  - system-observability
  - transparency
  - unicode
---

# Dream Cycle：不可见的产出与三层 Unicode 调试

**2026-05-15 — Telegram OpenClaw 会话**

## 起点：「13:00 cron 为什么没有可见产出？」

> 你查一下13:00 cron 为什么没有可见产出吧

第一层诊断结果是两个完全独立的原因叠加：

1. **Delivery 配置为 `none`**：13:00 的 Deep Dream Cycle 从未被配置成会推送 Telegram 消息。即使它成功跑完，也是静默的——`delivered: false / deliveryStatus: not-requested`。
2. **Synthesize 阶段本身失败**：`surrogates not allowed` 错误导致 transcript 没有处理成功，后续 `patterns skipped`、`consolidate 0`、`embed 0`、0 个页面写入。

这是一个「两个不同问题凑一块导致完全无产出」的案例。任何一个单独都会造成沉默，但根因完全不同。

## 追问来源：「你也要判断一下这个 transcript 的来源是哪里」

Henry 拿到诊断结论后，没有直接让 agent 去修，而是加了一个定位步骤：

> 好的，就按照你建议的两件事来做。同时你也要判断一下这个 transcript 的来源是哪里，是 Telegram 还是 Meetings 的信息。然后你去看一下对应的生成这些 transcript 的任务或者脚本，看看是不是那里面少考虑了一些情况。

这个问题最终查到：**不是 Telegram，也不是 OpenClaw 本身的普通文本；是 Get笔记/录音 transcript 里的说话人标记 emoji**。这个 emoji 本身是合法 Unicode，但经过 JS → JSON → LiteLLM/Python 这一链路时被转成 surrogate pair，低位变成非法 surrogate，LiteLLM 转发时没处理好，所以炸。

Henry 先问「来源是哪里」，然后才修脚本——这是「定位根因再修」的标准操作，避免了在错误的地方打补丁。

## 三层错误的实时剥离

这次调试是 [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] 模式的教科书案例，但特殊的是它在同一次会话里完整展示了三层：

| 阶段 | 错误 | 根因 |
|------|------|------|
| 第一轮 real run | `surrogates not allowed` | Get笔记 transcript 中的说话人标记 emoji，经 JS→JSON→Python 路径成为非法 surrogate |
| 第二轮 real run | `unsupported Unicode escape sequence` | OpenClaw transcript 里 `GPT\u00004o` 这类字面量，导出后变成真实 NUL 字符；Postgres `jsonb` 不接受 `\u0000` |
| dry-run 通过但 real run 失败 | dry-run 不写 Postgres，跳过 jsonb 校验层 | 测试环境和生产环境不覆盖相同的层 |

Agent 在第一层修完之后报告：

> 上一轮真实跑还是失败了，但错误已经从 `surrogates not allowed` 变成了新的 `unsupported Unicode escape sequence`。说明 emoji/surrogate 那层修掉了，下一层是 transcript 里还有某种非法 `\u...` 转义文本。

「错误变了」不是坏消息，是进度信号。这是调试多层系统的正确认知框架：每次错误变化都证明了上一个 fix 生效了，同时暴露了下一层。

## 结果：干跑通过，实跑待确认

修复三层后：

- 单元测试全过（33 个）
- `bun run typecheck` 无报错
- dry-run：`synthesize` 从 fail 变 ok，34 个 transcript 中 26 个进入合成

但按照 Henry 的明确要求，未触发真实 Dream Cycle 实跑。参见 [[originals/dream-cycle-governance-2026-05-15]] 中的「先确认再执行」治理原则。

完整的技术修复记录见 [[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]]。

---

*Source: Telegram session 2026-05-15, transcript hash 6b28a2*
