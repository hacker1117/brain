---
type: reflection
title: 四个需求的优先级讨论 & GBrain MCP Auth 修复过程
date: '2026-05-13T00:00:00.000Z'
tags:
  - debugging
  - gbrain
  - mcp
  - minimax
  - openclaw
  - reflection
  - requirements
---

# 四个需求的优先级讨论 & GBrain MCP Auth 修复过程

**Session:** 2026-05-13 05:14–05:54 UTC  
**Source:** OpenClaw Telegram session `1942833b` (transcript hash `876191`)

## Henry 的四个需求（原话）

> 我今天打算做 4 件事：
> * 我想把我跟客户日常沟通开会的整个流程整合到 gbrain 里，我主要是线上的飞书会议，和线下的会议（用 Getseed 录音，并做总结）。我希望这些信息能够比较方便和自动化的整合到 gbrain 里，这在现阶段会对我的工作有较大帮助，也会让你对我的理解迅速的变得更好
> * 我想要更近一步的去了解一下 gbrain，就是即使我现在已经把消息和其他的信息都收集进去了，怎么能保证你（Openclaw）以及其他的 Agent 在和我对话的时候，是可以用到对我的这些认识和理解的呢？我不想只是存储各种事实和理解，而这些信息不能被很好的应用
> * 我想在 Telegram 的 group 里，多个topics 的和你对话，并且在 group 里不需要@你，你就能回复，并且是能够用上 gbrain 里的一些 skill，包含你对我的一些理解，以及能把这些对话里的必要信息也都 Capture 到的
> * 第一个需求完成之后，我希望你能根据我在电商公司，和一个培训业务里的一些会议记录，和我一起来做技术方案和培训方案的讨论

## 商定的执行顺序

Agent 建议并 Henry 同意的顺序：

1. **需求 2（Agent 怎么用 GBrain 认知）** — 基础设施，先理清机制
2. **需求 1（会议记录自动入 GBrain）** — 依赖 2 的答案才能验证效果
3. **需求 3（Telegram group 多 topic）** — 牵扯最多，最后配
4. **需求 4（电商/培训方案讨论）** — 依赖前三都有数据支撑

**核心逻辑：** 先把「存进去的东西怎么被用上」搞清楚，再大量存数据才有意义。这和后来放弃需求 3 的决策（见 [[originals/telegram-group-abandoned]]）是一致的。

## GBrain 现状诊断（当时）

Agent 做了架构检查，发现关键问题：

| 层面 | 状态 |
|------|------|
| Signal Detector | ✅ 在跑（CLI，不走 MCP） |
| GBrain MCP 服务 | ✅ `localhost:3131` 在线 |
| OpenClaw → GBrain MCP auth | ❌ **完全没有 token** |
| MCP 工具注册 | ❌ 因为没有 auth，工具未注册 |
| 每次对话前查 GBrain | ❌ 无主动注入机制 |

> **说白了：** GBrain 是仓库，不是前扣（frontal cortex）。我每次回复都是靠 session 上下文临时拼出来的，GBrain 的内容不会自动浮现。

这个诊断呼应了之前 [[originals/brainops-calling-mechanism-exploration]] 里 Henry 的担忧——脑里存了东西，但 agent 不会自动用。

## GBrain 推荐的 Agent 循环（brain-agent-loop.md）

```
Signal arrives → DETECT entities (async)
              → READ: check brain FIRST (before responding)
              → RESPOND with brain context
              → WRITE: update brain pages
              → SYNC
```

这是[[concepts/gbrain-skill-brain-ops]] 里 READ → ENRICH → WRITE 循环的完整版本。问题是"每次回复前先读脑"没有被自动触发。

## MCP Auth 修复过程

这是本次 session 的主要技术工作：

### 发现的 bug
- `gbrain auth create` 命令有参数解析缺陷（v0.32.5 已知 bug：`takesIdx = -1` 时取到错误 positional）
- Agent 绕过 bug 手动生成 token

### 连接问题链
1. 先无 token → SSE 404
2. 加 token 后 → SSE `other side closed`（服务端兼容性）
3. 改 `streamable-http` → GBrain MCP 服务挂了（LaunchAgent plist 里 bun 路径坏了）
4. 重启 GBrain → 发现 Gateway 走代理访问 `127.0.0.1`，代理不支持本地连接

截至 05:54，Henry 在问「重启好了吗？现在 GBrain 和它的 MCP 服务都可以了吗？」—— 本 session 尚未到达最终可用状态。

## 值得注意的模式

- **本次用的模型是 `minimax/minimax-m2.7`**，与[[originals/minimax-bad-instruction-following-lesson]] 中的教训高度相关——这个 session 里 agent 多次做了错误判断（提前宣称"已可用"、没正确追踪服务状态），很可能就是弱模型指令遵循差的表现。
- Henry 有一个一致的 capture 验证习惯：「你确认一下，刚刚我发你的那个消息，有没有被 Capture」——他对 GBrain 的信任建立在可验证性上，而不是口头承诺。

---
*Related: [[originals/gbrain-setup-session-2026-05-12]] · [[originals/brainops-calling-mechanism-exploration]] · [[originals/brain-ops-hook-plugin-solution-deferred]] · [[concepts/gbrain-skill-brain-ops]] · [[originals/minimax-bad-instruction-following-lesson]]*
