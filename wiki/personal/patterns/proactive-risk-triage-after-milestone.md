---
type: concept
title: Proactive Risk Triage After Milestone
date: '2026-05-19T00:00:00.000Z'
tags:
  - debugging
  - infrastructure
  - pattern
  - risk-management
  - systems-thinking
---

# Pattern: Proactive Risk Triage After a Milestone — Don't Stop at "It Works"; Ask What's Still Broken

## Summary

Henry consistently does something that goes against the natural stopping impulse: **immediately after achieving a milestone — a system running for the first time, a fix that resolves the presenting problem, a successful live test — he proactively surfaces the next layer of risks rather than declaring victory and moving on**. The milestone is treated not as a stopping point but as a stable platform from which to look ahead: what wasn't tested by this run, what problems were visible but not yet prioritized, what could silently fail in the next automated run?

This is a distinct behavioral pattern from the diagnostic patience Henry applies *during* debugging (see [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]). That pattern is about pausing execution mid-problem to ensure correct diagnosis. This pattern is about pausing *after* resolution to proactively audit the remaining risk surface. The trigger is not a failure — it is a success that creates a new stability window.

The recurring structure: milestone achieved → immediate question "what else is still at risk?" → identify next-layer issues before they surface as failures → triage by impact → fix or document before the next real run.

---

## Observed Instances

### 1. Dream Cycle first live run: immediately asks about remaining infrastructure risks (2026-05-14)
[[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]]

After the Dream Cycle runs successfully for the first time (6 pages written and embedded — a genuine milestone), Henry does not close the session. He immediately raises three issues that were identified during the debugging process but not yet resolved:

> 「那除了夜间自动跑的时候的Worker的环境问题，你之前遇到的这个Timeout的问题，或者说还有一个并发等于一的问题，这些都还需要解决吗？它们核心会影响什么？」

The reflection notes explicitly:

> 「这个问题是主动诊断，不是对某个失败报错的被动反应——Dream 刚跑通，Henry 已经在识别下一层的结构风险。」

He correctly identifies three independent infrastructure gaps that the successful test did not exercise (because the test was manual, not the nightly automated worker path):

| Risk | Core Impact |
|------|------------|
| Worker missing `ANTHROPIC_*` env | Nightly runs appear to execute but LLM subagents silently fail |
| Timeout too short | Long synthesize tasks timeout; status is confusing (failed-but-running) |
| Worker concurrency = 1 | Long jobs block all other tasks; effective scheduling gap |

None of these would have surfaced from the successful manual test. Henry asked about them proactively — before the first nightly automated run could expose them as failures.

### 2. After synthesize fix: formalizes the "report first, then execute" protocol rather than proceeding (2026-05-15)
[[wiki/personal/reflections/2026-05-15-debug-report-first-then-execute-892ed1]]

After the Dream Cycle synthesize bug is fixed (surrogate → NUL → Postgres jsonb, multiple iterations), rather than simply authorizing the next test run, Henry formalizes a protocol that the repair process revealed was missing:

> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

This is proactive risk triage at the protocol level: the milestone (fixing synthesize) surfaces the observation that the agent had been iterating through real Dream Cycle runs with each fix, each one burning significant tokens. Rather than just fixing the bug and moving on, Henry uses the milestone as a prompt to establish a structural safeguard for all future debugging sessions. The protocol he documents becomes an asset that prevents the same cost pattern from recurring.

### 3. After infra fixes: demands verification plan before any test execution (2026-05-14)
[[wiki/personal/reflections/2026-05-14-dream-infra-verification-and-company-disclosure-12e3b1]]

After the agent completes a batch of infrastructure repairs (Worker env injection, timeout extension, concurrency increase), Henry's immediate question is:

> 「如果你已经改完了的话，我现在有什么办法来验证吗？不是通过手工的方式验证，而是来验证你当前的这个修复确实好用。」

> 「你先给我验证的方案哈，先不需要实际进行。」

The fixes are the milestone. Rather than immediately testing them (which would be the natural next step), Henry pauses to establish: what is the verification plan? He wants a scriptable, repeatable check — not a one-off manual confirmation. This is proactive risk triage at the verification level: before authorizing the execution, understand what "this fix worked" actually means and how to confirm it.

The distinction between "give me the plan" and "run the plan" is the proactive audit step: review the verification strategy before authorizing cost. This catches both "the fix might not work" risks *and* "the verification plan might be insufficient" risks — both of which would only surface during an actual run.

### 4. After Dream Cycle cost shock: proactively reconstructs cost distribution rather than accepting the first estimate (2026-05-15)
[[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]]

When the $30+ cost shock is discovered, Henry does not simply accept the agent's first estimate (~$5.9) and move on. He pushes back with ground truth:

> 「我今天实际花了30多美金的token，和你计算的不符」

This triggers a full cost reconstruction — tracing the actual spend across GBrain patterns subagents, Signal Capture calls, Dream report agent runs, and the synthesize backlog batch. The cost shock event is a milestone in the sense that the first estimate seemed to resolve the question; Henry's proactive challenge opens the next layer (where the actual money went).

The reconstruction reveals that the natural monitoring surface (cron log) was not the cost surface (subagent calls) — a structural insight that generates a new architecture recommendation (light/heavy task separation). The proactive push — rather than accepting the first satisfactory answer — is what surfaces the deeper structural issue.

---

## Pattern Structure

```
Milestone achieved (successful run, resolved bug, completed fix, first estimate)
  → Natural stopping point: "it works," "it's fixed," "the number is ~X"
  
  → Henry's actual behavior: treat milestone as a new stable platform
      → Ask: "what did this test NOT exercise?"
      → Ask: "what problems were visible during this session but not yet resolved?"
      → Ask: "what would cause the next automated run to fail silently?"
      → Ask: "is the first answer actually complete, or is it an approximation?"
  
  → Identify next-layer risks:
      → Categorize by impact: silent failure vs. visible failure vs. cost exposure
      → Prioritize: what must be resolved before the next real run?
      → Document: what is known to be at risk even if not immediately resolved?
  
  → Only then: authorize the next run / close the session / proceed

The pattern applies at multiple levels:
  → Technical (infra gaps remaining after a successful test)
  → Procedural (protocols missing after a debugging process surfaces their absence)
  → Financial (cost estimates that don't match ground truth)
  → Verification (fixes applied but not yet confirmed at the consumer level)
```

**The triggering heuristic:** When something works, ask: "What did this success *not* test?" The answer is always a non-empty set in a layered system. The question is whether to surface it now or wait for the next failure to surface it for you.

---

## Why This Pattern Matters

In a layered automation system like GBrain + Dream Cycle, a successful manual test exercises a *subset* of the system's actual operating conditions. Nightly automated runs use different paths (the worker process, not the manual trigger), different environments (no keyboard, different env vars), different volumes (accumulated transcripts, not a single test run). A milestone in the manual test path leaves the automated path's risk surface entirely un-examined.

Henry's proactive triage behavior is what ensures these remaining risks get surfaced and documented *before* they manifest as silent failures in production — which, in this system, means burning tokens without output for days before anyone notices (see [[wiki/personal/patterns/silent-success-masking-real-failure]]).

The behavioral pattern also has a protocol-generation effect: the "report first, then execute" protocol was not a pre-existing rule — it emerged from Henry noticing, after the synthesize fix milestone, that the debugging process had repeatedly iterated through expensive real runs. The milestone was the observation point; the proactive question was "what should have been different?"; the protocol was the answer.

This distinguishes Henry's approach from reactive debugging (respond to failures as they arrive) and moves him toward *preemptive architecture*: identifying and resolving structural risks during stable windows, before they force resolution under operational pressure.

---

## Connection to Adjacent Patterns

- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — this pattern is the post-milestone complement to diagnose-before-act. That pattern says "pause and diagnose before executing"; this pattern says "after a milestone, pause and audit before the next execution." Both insert explicit review points at cost/risk boundaries.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — the multi-layer surface pattern explains *why* proactive triage is warranted: a successful milestone at one layer leaves other layers unexamined, and real conditions will surface them sequentially. Proactive triage is the deliberate, advance version of multi-layer surfacing.
- **[[wiki/personal/patterns/silent-success-masking-real-failure]]** — proactive risk triage is specifically designed to prevent the worst case of silent failure: a system that appears to be running but is not producing useful output. By examining what the milestone didn't test (particularly nightly automated paths), Henry identifies the conditions under which silent failure can occur before they occur.
- **[[wiki/personal/patterns/human-in-the-loop-confirm-before-execute]]** — the confirm-before-execute protocol is a direct output of this pattern: the "report first, then execute" protocol was formalized *at* a post-fix milestone, in response to the proactive observation that the debugging process had been incurring unnecessary cost. The human-in-the-loop checkpoint is what proactive triage produces at the behavioral level.
- **[[wiki/personal/patterns/automation-cost-visibility-blind-spot]]** — proactive cost reconstruction (pushing back on the first estimate) is how the cost visibility blind spot gets corrected. Rather than accepting the cron-log view as accurate, Henry's challenge forces the full cost attribution exercise that reveals the actual spending structure.
- **[[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]** — proactive triage is the mechanism by which each GBrain session compounds: the milestone establishes a known-working layer; the triage identifies what the next layer requires; the next session can then start from that known base rather than from a surprise failure.

---

## Related Pages

- [[wiki/personal/reflections/2026-05-14-dream-worker-issue-triage-f6ddf1]]
- [[wiki/personal/reflections/2026-05-15-debug-report-first-then-execute-892ed1]]
- [[wiki/personal/reflections/2026-05-14-dream-infra-verification-and-company-disclosure-12e3b1]]
- [[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]]
- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/silent-success-masking-real-failure]]
- [[wiki/personal/patterns/human-in-the-loop-confirm-before-execute]]
- [[wiki/personal/patterns/automation-cost-visibility-blind-spot]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
