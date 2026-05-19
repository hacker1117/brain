---
type: concept
title: >-
  Pattern: Idempotency Guards Are Not Optional — Unguarded Automation Phases
  Generate Runaway Cost
date: '2026-05-15T00:00:00.000Z'
tags:
  - automation
  - cost
  - gbrain
  - idempotency
  - infrastructure
  - pattern
---

# Pattern: Idempotency Guards Are Not Optional — Unguarded Automation Phases Generate Runaway Cost

## Summary

When Henry discovers a cost explosion in an automated pipeline, the root cause is consistently the same: **one or more automation phases lacks idempotency protection** — a guard that prevents re-execution when the relevant work has already been done. The consequence is not just redundant computation; it is exponentially compounding cost as the unguarded phase reruns on every cycle.

Henry's diagnostic and remediation pattern is consistent: (1) identify which phase lacks protection, (2) analyze the existing protection mechanisms in other phases to establish the design template, and (3) add multi-dimensional guards (timestamp cooldown + content hash) to ensure the phase runs exactly once per meaningful input change.

This pattern is the negative complement of [[wiki/personal/patterns/automation-first-reduce-manual-activation]]: automation is essential, but every automated phase must be idempotency-protected, or it will consume unbounded resources.

---

## Observed Instances

### 1. Discovery: 30+ USD Sonnet bill from unguarded `patterns` phase (2026-05-14)
[[wiki/personal/reflections/2026-05-14-gbrain-autopilot-cost-explosion-2338b7]]

Henry discovers that the GBrain autopilot has consumed over $30 in Sonnet API fees — roughly an order of magnitude above his expected daily cost. His expectation baseline is Gary Tan (GBrain's creator), who manages more information and projects and does not incur equivalent overage.

Diagnostic summary:
- `gbrain autopilot` runs every 5 minutes (interval 300s)
- From midnight to the discovery time: 66 subagent jobs, 353 LLM calls
- The primary culprit: the `patterns` phase had **no idempotency protection at all**

> 每次 autopilot-cycle 都收集最近 30 天 reflections，只要 reflections 数量 >= 3，就提交一个 Sonnet subagent

The `patterns` phase ran a full Sonnet synthesis job on every cycle — regardless of whether the reflections had changed. In a 5-minute loop, this means ~288 unbounded Sonnet calls per day from this phase alone.

### 2. The contrast: `synthesize` phase had proper idempotency (2026-05-14)
[[wiki/personal/reflections/2026-05-14-gbrain-autopilot-cost-explosion-2338b7]]

The `synthesize` phase, by contrast, was protected by three complementary guards:
- `dream.synthesize.last_completion_ts` — tracks last run time
- 12-hour cooldown — prevents re-run within 12h of last completion
- Transcript content hash — prevents re-processing the same source file

This multi-dimensional guard structure is the design template. The `patterns` phase had none of it.

### 3. Fix: adding idempotency to `patterns` + architectural separation of light/heavy tasks (2026-05-14)
[[wiki/personal/reflections/2026-05-14-autopilot-cost-fix-and-scheduling-architecture-185e90]]

After identifying the root cause, Henry oversees a two-pronged remediation:

**Immediate fix**: add idempotency guards to the `patterns` phase:
- Last-run timestamp tracking
- Cooldown period
- Content hash to detect meaningful input change (reflections added or changed)

**Architectural fix**: separate light tasks (quick polling, status checks) from heavy tasks (Sonnet synthesis) into different scheduling tiers, so that high-cost LLM work is not triggered at the same frequency as lightweight checks.

The fix is not just "add a cooldown" — it is a structural redesign of the automation architecture to respect the cost asymmetry between phases.

### 4. Proactive infrastructure triage: identifying the next unguarded phases (2026-05-14)
[[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]]

After the Dream Cycle first ran successfully, Henry immediately asks about the remaining unresolved infrastructure issues — not because something had just broken, but as proactive risk identification:

> 「那除了夜间自动跑的时候的Worker的环境问题，你之前遇到的这个Timeout的问题，或者说还有一个并发等于一的问题，这些都还需要解决吗？它们核心会影响什么？」

This is the idempotency-guard pattern applied prospectively: before a cost explosion or silent failure can occur, Henry maps the remaining unprotected phases and classifies their impact. The Worker concurrency=1 issue, for example, means that if a long synthesize task is picked up by a worker, it blocks all other autopilot tasks — a hidden single-threaded bottleneck in what should be parallel automation.

---

## Pattern Structure

```
Automated pipeline runs on a recurring schedule
  → Every phase MUST have idempotency protection:
      Minimum viable guard: last_run_timestamp + cooldown
      Stronger guard: last_run_timestamp + cooldown + content hash
      Strongest: all three + explicit skip logging

  → Missing guard = unbounded cost accumulation at schedule frequency
      e.g., 5-min cycle × 288 runs/day × Sonnet cost = 10x expected spend

  → When a cost explosion is discovered:
      1. Identify which phase lacked idempotency
      2. Map existing guards in other phases (use as template)
      3. Add: timestamp + cooldown + content hash
      4. Audit ALL other phases for the same gap — partial fixes leave adjacent risk

  → Architectural principle:
      Heavy tasks (LLM synthesis) → low-frequency tier
      Light tasks (status polling, quick checks) → high-frequency tier
      Never conflate frequency tiers in a single schedule loop
```

**The diagnostic question:** "If this phase ran 288 times today with the same inputs, how much would it cost?"
- Acceptable → phase is either cheap or idempotency-protected
- Unacceptable → add guards immediately

---

## Connection to Adjacent Patterns

- **[[wiki/personal/patterns/automation-first-reduce-manual-activation]]** — automation without idempotency is not a solved problem; it is a deferred cost explosion. Idempotency guards are the safety layer that makes automation safe to run continuously.
- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — the cost discovery triggers the same diagnostic loop: stop, map root cause across all phases, fix all instances, not just the presenting one.
- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — an unguarded automation phase is anti-compounding: it burns cost without producing marginal value. Idempotency guards are what ensure the automation's value compounds rather than its cost.
- **[[wiki/personal/patterns/foundations-first-sequencing]]** — idempotency guards are a foundational requirement for any automation phase; building higher-level capabilities on unguarded foundations creates invisible cost risk that surfaces only when the system is under real load.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — the cost explosion was discovered during a real-use window (overnight autopilot run), not during testing. Real conditions surface this class of problem; tests rarely do, because test environments don't run 288 cycles.

---

## Related Pages

- [[wiki/personal/reflections/2026-05-14-gbrain-autopilot-cost-explosion-2338b7]]
- [[wiki/personal/reflections/2026-05-14-autopilot-cost-fix-and-scheduling-architecture-185e90]]
- [[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]]
- [[wiki/personal/patterns/automation-first-reduce-manual-activation]]
- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/compounding-investment-over-transactional-use]]
- [[wiki/personal/patterns/foundations-first-sequencing]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
