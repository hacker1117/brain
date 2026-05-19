---
type: concept
title: 2026 05 15 Dream Cycle Unicode Cascade And Preflight Protocol 5e0c70
date: '2026-05-15T00:00:00.000Z'
source: openclaw-session
session_id: f1697da1-46ec-4cb0-ab22-90d067dc80e6
transcript_hash: 5e0c70
tags:
  - cost-awareness
  - debugging
  - dream-cycle
  - gbrain
  - infrastructure
  - preflight
  - unicode
---

# Dream Cycle Synthesize 三层 Unicode 错误联调，与"报告后再执行"的复原协议

**2026-05-15 — Telegram OpenClaw 会话 `f1697da1`**

## 起点：无可见产出的 cron

上午 Henry 发现 13:00 的 Dream Cycle cron 没有任何产出。排查后发现两个问题叠加：

1. **delivery 配置为 `none`** — 即使 cron 成功完成，也不会主动推送 Telegram 通知（`delivered: false / deliveryStatus: not-requested`）
2. **synthesize 阶段持续失败** — brain 里 0 个页面更新

这是"隐形失败"的典型形态：cron 在 scheduler 层面显示 `ok`，但没有任何可观察输出，错误只存在于日志深处。一个 `delivery: none` 的失败 cron，比根本不跑的 cron 更危险——它烧了 token，但连失败信号都不传递。

## 三层 Unicode 错误的逐层剥开

修复 synthesize 的过程是[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]的典型案例：每修一层，新一层错误才浮出。

### 第一层：emoji surrogate（`surrogates not allowed`）

- **来源**：Get笔记/录音 transcript 里的说话人标记 emoji（如 `🟢`）
- **路径**：JS → JSON → LiteLLM/Python，emoji 被转为 surrogate pair，低位非法
- **表现**：`synthesize: fail`，`surrogates not allowed`
- **修复**：export 脚本 + ingest pipeline + transcript-discovery.ts 三层 sanitize

第一层修掉后，dry-run 通过（`synthesize: ok`，34 个 transcript 里 26 个进入合成）。但**真实跑仍失败**，暴露了第二层。

### 第二层：`\u0000` / NUL 字符（Postgres jsonb 拒绝）

- **来源**：OpenClaw transcript 里有 `GPT\u00004o` 这样的字面量，导出后变成真实 NUL 字符
- **路径**：Postgres `jsonb` 不支持 `\u0000`
- **表现**：`unsupported Unicode escape sequence`
- **诊断线索**：dry-run 不写 Postgres，所以它过了；real run 写 jsonb 时炸

这一层揭示了 dry-run 和 real-run 之间的结构性盲区——dry-run 的"成功"只覆盖了不写数据库的那些层。参见[[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]]。

### 第三层：`put_page {}` 空参数导致 token 循环消耗

- **来源**：synthesize subagent 调用 `brain_put_page` 时 input 为 `{}`（空参数）
- **后果**：`slug.startsWith(...)` 报 `TypeError: undefined is not an object`
- **关键点**：这个错误不立刻终止 agent，而是被喂回模型继续尝试 → job 490 单次消耗约 679k input tokens
- **修复**：`put_page` 入口加强校验（无 slug / content 非 string → `invalid_params`），subagent `max_turns: 30 → 8`，timeout `30min → 12min`

三层加在一起才是今天实际花费 $30+ 而非估算 $5.9 的完整解释。参见[[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]]。

## Henry 的指令：先报告，后执行

修复代码后，Henry 明确设定了一个协议：

> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

这是[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]在自动化系统管理场景下的直接应用：修代码 → 静态验证（unit test + typecheck） → 汇报问题和修复方案 → 人工确认无风险 → 才执行。

同一次对话中他还做了另一个决策：

> 「整体的 Dreamcycle 我也不希望一天执行两次了，我希望只在晚上 2 点执行一次。」

原来 13:00 和 02:00 各一次的设计，在调试过程中暴露为隐性冗余：13:00 那次从未产生有效产出（delivery none + 持续失败），但一直在算 token 额度。

## 修复的验证边界

Agent 的最终报告：

- 33 个单元测试全过（`bun test test/operations-allow-list.test.ts test/brain-allowlist.test.ts`）
- `bun run typecheck` 无报错
- dry-run：`synthesize: ok`，26/34 transcripts 进入合成
- Dream Cycle cron 临时暂停（保留 `02:00 Asia/Shanghai` schedule，等 Henry 确认后恢复）
- **未触发真实 Dream Cycle 实跑**

## Preflight 协议建议

Agent 在本次对话末尾提出了一个新的操作模板：

> 建议下一步不是直接恢复 cron，而是先做一次低成本 preflight：只统计下一轮会处理多少 transcript、预计多少 child jobs，不调用 Sonnet。确认规模没问题，再启用 02:00 或手动跑一轮。

这是对"先报告再执行"协议的具体实现形式：用一次零/低成本的 preflight 探测，把"下一轮真实跑的成本估算"从盲盒变成可见数字，再由 Henry 决策是否放行。

这个形态也是[[wiki/personal/patterns/foundations-first-sequencing]]的延伸：不在未验证规模的基础上直接启动资源密集型任务。

---

*Source: Telegram/OpenClaw session 2026-05-15, transcript hash 5e0c70*  
*Related: [[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]] · [[originals/dream-cycle-synthesis-failure-root-cause-2026-05-15]] · [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]] · [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]] · [[wiki/personal/patterns/foundations-first-sequencing]]*
