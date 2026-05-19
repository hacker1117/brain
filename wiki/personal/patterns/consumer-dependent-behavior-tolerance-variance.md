---
type: concept
title: Consumer Dependent Behavior Tolerance Variance
date: '2026-05-14T00:00:00.000Z'
tags:
  - architecture
  - compatibility
  - debugging
  - pattern
  - systems-thinking
---

# Pattern: Consumer-Dependent Behavior — Hidden Design Debt Exposed by Stricter Consumers

## Summary

A recurring failure mode in Henry's infrastructure sessions: a system or component that has been working correctly **under one consumer's permissiveness** suddenly breaks the moment a stricter or differently-configured consumer tries to use it. The failure is not new — the underlying design defect existed all along. The permissive consumer was simply tolerating it silently. Switching consumers doesn't introduce a bug; it **reveals pre-existing debt** that the original consumer was hiding.

This pattern explains why Henry consistently treats a "model switch" or "protocol change" or "manual trigger" not as an operational event but as a **diagnostic probe**: a stricter consumer will surface what a permissive one concealed. The correct response is not to revert to the tolerant consumer — it is to fix the underlying defect to the strictest standard, so the system is portable and trustworthy across all consumers.

---

## Observed Instances

### 1. Soul Audit → agent cognition gap: session context as the permissive "consumer" (2026-05-12)
[[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]

Henry seeds GBrain with rich self-knowledge during the Soul Audit. The agent (OpenClaw) continues operating correctly in conversations — but not because it is using GBrain. It is using session context alone, implicitly tolerating the absence of any live GBrain connection. Session context is the **permissive consumer**: it works without MCP auth, without the READ → ENRICH → WRITE loop, without any structured retrieval. It simply uses whatever is in the conversation window.

The defect — GBrain is a warehouse, not a frontal cortex — was fully present from day one. The session-context consumer never surfaced it because it had no requirement for GBrain-backed reasoning. Only when Henry explicitly asked "how is GBrain knowledge being used?" did the design gap become visible:

> 「GBrain 是仓库，不是前扣（frontal cortex）。我每次回复都是靠 session 上下文临时拼出来的，GBrain 的内容不会自动浮现。」

Henry does not treat the discovery as a surprise failure — he treats it as an accurate reading of system state. The correct response is not to stop seeding GBrain (revering to session-context-only), but to fix the retrieval and cognition pipeline so that any consumer — session, MCP, future agent — receives knowledge-backed responses by default.

### 2. Claude → Codex model switch: JSON Schema strictness reveals missing `items` (2026-05-14)
[[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]

Henry switches OpenClaw's model from Claude to OpenAI Codex (gpt-5.5). Immediate failure:

> `LLM request rejected: Invalid schema for function 'gbrain__extract_facts': In context=('properties', 'entity_hints'), array schema missing items.`

The schema had been *wrong by spec* since it was written — `entity_hints` declared as `type: 'array'` with no `items` field. Claude's tool-call parser tolerated this silently and worked correctly. Codex strictly validates against the JSON Schema specification and rejects non-compliant schemas.

The defect was not caused by the model switch. The model switch made it visible. The reflection states this explicitly as a technical lesson:

> **Claude 对不完整 schema 容忍度高，OpenAI/Codex 严格按 JSON Schema spec 校验。维护 MCP 工具时，schema 需要按最严格标准写。**

Henry does not revert to Claude to "fix" the problem. He traces and fixes the schema to the strictest standard — making it portable across all present and future model consumers.

### 3. Manual trigger vs. autopilot trigger: lock semantics differ by initiator (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

When Henry manually triggers the Dream Cycle, it immediately hits a `cycle_already_running` lock conflict — autopilot-cycle 259 is already executing. The normal flow (autopilot triggers only) had never exposed this: the autopilot only runs one cycle at a time and never contends with a manual trigger.

The manual trigger is a *different consumer* of the same locking system, with different assumptions about exclusivity:
- The autopilot assumes it is the only initiator and queues correctly
- Manual triggers have no awareness of autopilot state and attempt concurrent execution

The locking system had a hidden assumption: **only one class of trigger exists**. The moment Henry becomes a second class of consumer (manual), the assumption fails visibly. The fix required coordinating both trigger types — waiting for the autopilot cycle to complete before issuing the manual trigger.

### 4. SSE → streamable-HTTP protocol switch: each protocol exposes a different failure layer (2026-05-13)
[[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]

Establishing OpenClaw → GBrain MCP connectivity required switching protocols, with each protocol acting as a different "consumer" of the underlying transport layer:

- **SSE** (first attempt): returned `other side closed` — the server-side SSE implementation had compatibility issues with the connection style
- **streamable-HTTP** (second attempt): GBrain MCP service *crashed* — a completely different failure, this time in the LaunchAgent plist's `bun` executable path
- **Post-restart** (third attempt): OpenClaw Gateway was proxying `127.0.0.1` through a system proxy that did not support localhost connections

Each protocol switch revealed a pre-existing failure mode that the previous protocol had been obscuring. The system was not "working" under SSE — it was failing silently or in a less visible way. The stricter or differently-routed consumer (streamable-HTTP, then direct connection, then proxy) exposed what each prior configuration was papering over.

---

## Pattern Structure

```
System S works under consumer C1 (permissive / lenient / tolerant)
  → C1 does not validate, silently ignores, or works around defects in S
  → Latent defect in S is invisible

Consumer C2 is introduced (strict / different protocol / different initiator)
  → C2 validates correctly, requires spec-compliant behavior, or has different assumptions
  → Defect in S immediately fails

Incorrect response: revert to C1 to restore "working" state
  → This hides the defect again
  → The system remains fragile: any future strict consumer will re-expose it

Correct response: treat C2's failure as a diagnostic gift
  → Fix S to the strictest consumer's standard
  → S becomes portable: it works under C1, C2, and any future C3
  → Latent debt is eliminated rather than hidden
```

**Consumer pairs that commonly exhibit this dynamic:**

| Permissive Consumer | Strict Consumer | What Gets Exposed |
|---------------------|-----------------|-------------------|
| Session context only | MCP-connected agent | GBrain warehouse–frontal cortex gap |
| Claude (tool-call parser) | OpenAI/Codex (strict JSON Schema) | Missing `items`, incomplete array schemas |
| Autopilot trigger only | Manual + autopilot triggers | Lock contention, hidden single-initiator assumptions |
| SSE protocol | streamable-HTTP, direct connection | Protocol compatibility gaps, proxy localhost restrictions |
| Local `.env` | LaunchAgent plist + process inheritance | Env var multi-copy divergence |

---

## Why This Matters

This pattern is particularly insidious because the system appears to be working correctly — **until it doesn't**. The session-context consumer never surfaced that GBrain wasn't being used. Claude never complained about the missing `items`. The Dream Cycle ran fine under autopilot-only conditions. The connection attempt under SSE produced *some* response rather than complete silence. In each case, the permissive consumer was masking a latent defect rather than surfacing it.

Henry's response across all four sessions is consistent: he does not treat the stricter consumer as the problem. He treats it as **the first honest reporter** — the consumer that is telling him the truth about his system's actual state. Reverting to the tolerant consumer would be epistemically regressive: choosing comfortable blindness over accurate knowledge.

The pattern also explains why Henry systematically favors **real-condition tests over simulated ones** (see [[wiki/personal/patterns/experiment-driven-decision-making]]): simulated tests often are themselves permissive consumers. They are designed to work under controlled conditions. Only real consumers — models that actually validate, users who actually trigger manually, protocols that actually enforce transport semantics — surface what the simulation was tolerating.

Structurally, this pattern is closely related to [[wiki/personal/patterns/hidden-layers-silent-corruption]]: both involve a pipeline where the final consumer is not receiving correct data, but neither the source nor any intermediate layer is throwing a visible error. The difference is emphasis — hidden-layers focuses on *where* the data is lost in the pipeline; this pattern focuses on *why* the defect was invisible (consumer tolerance) and what switching consumers reveals.

---

## Relationship to Adjacent Patterns

- **[[wiki/personal/patterns/hidden-layers-silent-corruption]]** — structural sibling. Hidden layers silently *drop* correct inputs; permissive consumers silently *accept* incorrect inputs. Both result in a system that appears to work while containing latent defects. Together, they cover the two directions of silent failure.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — consumer switches often are the "first real test" events: a model switch, a manual trigger, a protocol change are all real-condition probes that surface multiple hidden layers simultaneously. This pattern explains *why* those first real tests are so productive: the new consumer is stricter.
- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — before accepting that a system works, Henry verifies at the consumer, not just the source. This pattern identifies one reason why source-level verification can be misleading: the source may be producing correct output that a permissive consumer is tolerating despite spec violations.
- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — fixing to the strictest standard compounds: every future consumer works correctly without a debugging session. Reverting to the tolerant consumer is anti-compounding: the debt remains and will be re-surfaced by the next strict consumer.
- **[[wiki/personal/patterns/experiment-driven-decision-making]]** — consumer switches are deliberate or accidental experiments. Henry treats the Codex switch as a probe, accepting its failure output as signal. The information value (schema is non-compliant) is more valuable than the operational disruption.
- **[[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]** — the infrastructure Henry is building must be multi-consumer from the start: multiple models, multiple trigger sources, multiple protocols. This pattern is the recurring cost of discovering single-consumer assumptions baked into early implementations.
- **[[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]** — the Soul Audit instance of this pattern is the clearest expression of the thinking-partner goal: a session-context-only agent is the "permissive consumer" that appears to work while actually missing the entire value proposition of GBrain as a long-term cognition partner.

---

## Related Pages

- [[wiki/personal/patterns/hidden-layers-silent-corruption]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/compounding-investment-over-transactional-use]]
- [[wiki/personal/patterns/experiment-driven-decision-making]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
- [[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]
- [[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]
- [[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]
