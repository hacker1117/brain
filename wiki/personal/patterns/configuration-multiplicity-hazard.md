---
type: concept
title: Configuration Multiplicity Hazard
date: '2026-05-14T00:00:00.000Z'
tags:
  - configuration
  - debugging
  - infrastructure
  - pattern
  - systems-thinking
---

# Pattern: Configuration Multiplicity Hazard — "I Changed It" Means Nothing Until You Know Which Copy

## Summary

A recurring failure mode across Henry's infrastructure sessions: **the same configuration value exists in multiple simultaneous representations** — an `.env` file, a LaunchAgent plist, a running process's inherited environment, a gateway's startup cache, a config store keyed to the wrong database, or a knowledge base that exists in storage but not in the agent's live cognition. When Henry or an agent changes a setting, the change lands in one representation, and one or more others silently continue serving the old value. The system does not error; it simply runs on stale config. The surface appearance is that the change was made; the operational reality is that the consumer never received it.

This is not a single class of bug — it is a **structural hazard of distributed configuration**: any time a system reads its configuration from multiple sources that are not atomically synchronized, divergence is possible and will eventually occur. This hazard is not confined to technical config files — it also manifests in the cognitive architecture of the agent system itself.

---

## Observed Instances

### 1. Soul Audit: Knowledge stored in GBrain ≠ knowledge in agent cognition (2026-05-12)
[[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]

Henry completes a thorough Soul Audit, seeding GBrain with identity, values, communication preferences, career history, and decision philosophy. Two representations of "what the agent knows about Henry" now exist simultaneously:

- **GBrain storage** — fully populated with rich self-knowledge
- **Agent cognition pipeline** — empty; session context only; no MCP connection

The agent (OpenClaw) continued operating from session context, producing answers with no access to GBrain. From Henry's view: "I told it everything about me." From the system's view: the agent's active cognition had received nothing. No error was thrown. The agent didn't say "I don't have your context" — it responded as if the session window was sufficient.

Discovery (next day):

> 「GBrain 是仓库，不是前扣（frontal cortex）。我每次回复都是靠 session 上下文临时拼出来的，GBrain 的内容不会自动浮现。」

This is configuration multiplicity at the cognitive layer: two representations of "the agent's knowledge of Henry" — one correct (GBrain), one stale (session only) — diverged silently. The fix required building the MCP connection so that all reads from "what do I know about Henry?" went through a single source of truth.

### 2. `DATABASE_URL` applied to wrong config store — Dream Cycle silently hits wrong database (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

When attempting to override `dream.synthesize.cooldown_hours`, the config change was written without also specifying `GBRAIN_DATABASE_URL`. The write landed in a non-Supabase config representation — the Supabase instance (the actual target) never received the change. From Henry's perspective: "I changed the setting." From the system's perspective: nothing changed.

The reflection identifies this as a **persistent architectural risk**:

> 「改了 GBrain autopilot 的环境变量脚本，得让 LaunchAgent 重新载入一次，否则正在跑的autopilot 进程未必拿到新的 Anthropic/LiteLLM 路由。」

The config existed in at least three simultaneous representations:
- The `.env` file on disk
- The LaunchAgent plist (which defines the startup environment for the autopilot process)
- The live environment of the running autopilot process (inherited at fork time from the plist)

A change to the `.env` file does not propagate to the plist; a change to the plist does not propagate to a live process until it is restarted. These three copies can independently diverge — and all three can appear "correct" when inspected in isolation.

### 3. OpenClaw Gateway serving stale schema from startup cache (2026-05-14)
[[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]

After fixing `entity_hints` in `operations.ts` and the transform whitelist in `serve-http.ts`, the JSON Schema bug still persisted in OpenClaw. The OpenClaw Gateway had fetched and cached GBrain's schema at startup — before the fix was applied — and was serving this stale version to Codex.

Two representations of "the current schema" existed simultaneously:
- GBrain's live schema endpoint (now correct after fixes + restart)
- OpenClaw Gateway's in-memory startup cache (still the old, broken version)

Neither threw an error. The source said "correct"; the consumer received "incorrect." The fix required explicitly restarting the Gateway to flush the cache:

```
operations.ts (fixed) → serve-http.ts (fixed) → GBrain restart → openclaw gateway restart → ✅
```

The full fix chain required understanding that restarting GBrain alone was insufficient — the Gateway had its own copy that required its own restart.

### 4. LaunchAgent plist bun path divergence — MCP service crashes silently on protocol switch (2026-05-13)
[[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]

When switching MCP protocol from SSE to `streamable-http`, the GBrain MCP service crashed unexpectedly. The root cause was unrelated to the protocol switch: the LaunchAgent plist had a broken `bun` executable path. The service had been running under SSE without this being visible (possibly because SSE's startup path didn't exercise the same code path, or the crash mode was masked).

This is the multiplicity pattern in a different form: the *intended* executable path in the developer's mental model diverged from the *actual* path recorded in the plist. The plist was a separate representation of "where bun is" that had gone stale relative to the system's actual bun installation location.

The protocol switch — itself unrelated to the path — acted as a precipitating event that forced a code path where the stale plist entry was exercised for the first time.

---

## Pattern Structure

```
Configuration value V exists in representations R1, R2, R3...
  → Developer changes V in R1
  → R2 and R3 still hold old V
  → Consumer reads from R2 → receives old V
  → No error is thrown
  → System appears to accept the change (R1 was correctly modified)
  → System behaves as if the change never happened

Common representation pairs that diverge:
  GBrain storage            ↔ agent cognition pipeline (requires MCP + READ loop)
  .env file                 ↔ LaunchAgent plist
  LaunchAgent plist         ↔ live process environment (inherited at fork)
  Source definition         ↔ transform layer whitelist
  Transform layer           ↔ gateway/consumer startup cache
  Config change API         ↔ database target (if wrong DATABASE_URL → wrong db)
  Developer's mental        ↔ actual path recorded in plist / config file
    model of the path

The correct diagnostic question after "I changed it but it didn't take effect":
  → WHERE is the consumer reading this value FROM?
  → Did the change land in that representation?
  → What is the propagation mechanism? (restart / reload / cache flush / env inheritance / MCP connection)
  → Have I verified at the consumer, not just at the source?
```

**Propagation mechanisms required per representation type:**

| Representation Changed | What must also happen |
|------------------------|----------------------|
| GBrain storage (knowledge base) | MCP connection established; READ loop activated |
| `.env` file | LaunchAgent plist must be updated AND process restarted |
| LaunchAgent plist | `launchctl unload` + `launchctl load` (or process restart) |
| Source code definition | Service restart |
| Transform/middleware layer | Middleware layer restart |
| Gateway startup cache | Gateway restart |
| Config API (without DATABASE_URL) | Re-apply with correct database target |

---

## Why This Matters

Configuration multiplicity is especially insidious for two reasons:

**1. It produces false confidence.** After applying a change, the developer verifies it at the source — and the source looks correct. No error is thrown. The fix appears complete. The bug persists silently. This is the same class of failure as [[wiki/personal/patterns/hidden-layers-silent-corruption]], but the mechanism is different: the intermediate "layer" here is not a transform or cache in the data pipeline, but a *separate, independent copy of the same configuration value*. Both patterns share the diagnostic cure: verify at the consumer, not the source.

**2. It scales with system complexity.** A single-process system with one `.env` file has low multiplicity risk. GBrain's architecture — LaunchAgent autopilot process + GBrain server + OpenClaw Gateway + per-session agent processes + the agent cognition pipeline itself — means that every config value potentially exists in 3–5 representations simultaneously. Every new component added to the stack adds new potential for divergence.

Henry's characteristic response across all four instances is consistent: **do not declare a fix complete until you have verified that the consumer is reading the corrected value**. In the Soul Audit case, the fix wasn't done until MCP auth was working and the agent was confirmed to be retrieving GBrain context. In the Dream Cycle session, the fix wasn't done until the cycle actually ran to completion with the correct database. In the schema session, the fix wasn't done until Codex successfully called the tool without rejection. In the MCP session, each fix exposed the next stale representation.

The lesson Henry draws in the Dream Cycle reflection — that changing the LaunchAgent plist requires an explicit reload — is the general principle: **every representation has its own propagation latency and explicit refresh mechanism, and none of them propagate changes to others automatically**. This includes the non-obvious cognitive case: seeding GBrain's knowledge base does not automatically propagate into the agent's live reasoning — that propagation requires a live connection.

---

## Relationship to Adjacent Patterns

- **[[wiki/personal/patterns/hidden-layers-silent-corruption]]** — structural sibling. Hidden layers *silently transform or discard* data in transit; configuration multiplicity means the data *never entered the pipeline* because a stale representation was read instead. Both result in "I fixed it but it's still broken." The diagnostic cure is the same: trace to the consumer and verify there, not at the source.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — configuration multiplicity is one of the most common *types* of hidden layers that first real tests surface. Under simulated or partial tests, only one representation may be exercised; the first real end-to-end run exercises all of them and surfaces the divergences.
- **[[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]** — a related trigger: when a new, stricter consumer (Codex, a different protocol, a manual trigger, or a properly MCP-connected agent) reads the same pipeline, it may exercise a code path that references a stale representation that the previous consumer never touched. The permissive consumer was unintentionally working around the stale config; the strict consumer exposes it.
- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — Henry's demand for verifiable evidence is the behavioral response to this pattern: "I changed it" is not evidence; "the consumer received the new value and behaved accordingly" is evidence. Henry enforces verification at the consumer level specifically because source-level verification is insufficient under multiplicity.
- **[[wiki/personal/patterns/automation-first-reduce-manual-activation]]** — stale configurations that require manual restarts or reloads to propagate are automation gaps: the system cannot autonomously keep its representations synchronized. Fixing the propagation mechanism (rather than accepting manual restart as a permanent workflow) is the automation-first response.
- **[[wiki/personal/patterns/foundations-first-sequencing]]** — configuration correctness is a foundational layer. A higher-level fix (schema definition, cooldown setting, agent reasoning quality) built on a misconfigured lower layer (wrong database, stale cache, disconnected GBrain) will appear to work while silently doing the wrong thing. Foundations-first requires verifying that the config reached the right process before trusting that higher-level logic will behave correctly.
- **[[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]** — the Soul Audit instance is the most abstract form of this pattern: the "configuration" is self-knowledge; the two representations are GBrain storage vs. agent cognition; and the multiplicity hazard is what stands between a knowledge warehouse and a genuine thinking partner. The fix — MCP connection + READ loop — is how the representations are synchronized.

---

## Related Pages

- [[wiki/personal/patterns/hidden-layers-silent-corruption]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]
- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/automation-first-reduce-manual-activation]]
- [[wiki/personal/patterns/foundations-first-sequencing]]
- [[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
- [[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]
- [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]
