---
type: concept
title: >-
  Pattern: Human-in-the-Loop — Insert a Confirmation Checkpoint Before High-Cost
  or High-Risk Execution
date: '2026-05-18T00:00:00.000Z'
tags:
  - agent-autonomy
  - cost
  - human-in-the-loop
  - infrastructure
  - pattern
  - risk
---

# Pattern: Human-in-the-Loop — Insert a Confirmation Checkpoint Before High-Cost or High-Risk Execution

## Summary

Henry grants agents wide operational latitude under normal conditions — he regularly says "好的，就按照你建议的两件事来做" (fine, do the two things you suggested) without requiring explanation. But when the operation involves **real cost, real data mutation, or non-reversible side effects**, he consistently inserts a hard checkpoint: the agent must **report what it found and what it plans to do** before executing, not after. This is not distrust of the agent's technical judgment. It is an explicit architectural decision about where the human decision boundary sits in high-stakes automation loops.

The recurring structure is: **report → discuss → confirm → execute**, with the "discuss and confirm" step being the invariant. The pattern surfaces repeatedly — across debugging sessions, infrastructure changes, and cost-sensitive pipeline runs — and is often explicitly stated by Henry in response to a recent cost event or an upcoming irreversible action.

---

## Observed Instances

### 1. Cost shock triggers the "先确认再执行" protocol (2026-05-15)
[[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]]

After discovering that today's real Dream Cycle cost was $30+ (versus an estimated ~$5.9), Henry establishes a formal checkpoint requirement:

> 「先确认再执行」

The cost event itself was triggered by an agent iterating through multiple real Dream Cycle runs during a debugging session — each run consumed significant tokens. Henry's response is not to stop using agents for debugging, but to make the confirmation step **explicit and mandatory** before any real execution begins.

### 2. Explicit "report first, then run" debugging protocol (2026-05-15)
[[wiki/personal/reflections/2026-05-15-debug-report-first-then-execute-892ed1]]

After spending $30+ on iterative Dream Cycle debugging (surrogate → NUL → Postgres jsonb, each fix triggering a new real run), Henry sets a hard rule:

> 「这次你修完刚才那个 Synthesis 的问题之后，先不用启动 Dreamcycle 测试。你告诉我你查到了什么问题，怎么解决的。然后我们讨论的确实没有风险之后，再执行下一轮。」

The protocol has three stages:
1. **Report**: what did you find, what did you change?
2. **Discuss**: is there residual risk? is the fix correct?
3. **Execute**: only after both parties agree there is no remaining risk

This is distinguished from Henry's normal mode (high agent autonomy) by the **cost and irreversibility** of real Dream Cycle runs. Low-risk tasks still get full delegation.

### 3. Verification plan before action — infra fixes (2026-05-14)
[[wiki/personal/reflections/2026-05-14-dream-infra-verification-and-company-disclosure-12e3b1]]

After the agent completes a batch of infrastructure fixes (Worker env injection, timeout extension, concurrency adjustment), Henry's first response is not "run a test" — it is "give me a verification plan first":

> 「如果你已经改完了的话，我现在有什么办法来验证吗？不是通过手工的方式验证，而是来验证你当前的这个修复确实好用。」

> 「你先给我验证的方案哈，先不需要实际进行。」

The separation of "give me the plan" from "run the plan" is the confirmation checkpoint in operation. Henry refuses to collapse plan and execution into one action — he wants to review the verification strategy before authorizing the real test.

### 4. Explicit cost acceptance before authorizing real Dream run (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

When Henry decides to authorize the first full real-cost Dream Cycle run, he does so explicitly:

> 「你现在来手动跑一遍完整的Dream流程吧。我需要实际的测试一下，我可以接受里面的成本。」

The phrase "我可以接受里面的成本" is the confirmation checkpoint spoken aloud — he is making a conscious, acknowledged decision to authorize real cost expenditure, not delegating that decision implicitly to the agent. The run proceeds because Henry has actively consented, not because the agent decided the time was right.

---

## Pattern Structure

```
Normal operations (low cost / reversible):
  → Agent has wide latitude; Henry delegates fully
  → "好的，就按照你建议的两件事来做"

High-cost / high-risk / irreversible operations:
  → STOP before execution
  → Agent: report what you found, report what you plan to do
  → Human: review; discuss risk; verify no remaining unknowns
  → Human: explicitly authorize ("我可以接受里面的成本" / "再执行下一轮")
  → Agent: execute

The checkpoint is:
  - NOT a sign of distrust in the agent's technical capability
  - NOT blanket micromanagement
  - A targeted gate on operations where the cost of being wrong is high
    (financial cost, data mutation, non-reversibility)

The trigger conditions for inserting a checkpoint:
  - Real API cost > threshold
  - Non-reversible system state change (data write, cron reset, cooldown override)
  - New infrastructure territory (first live run of a new pipeline)
  - Recent cost event or failure (checkpoint inserted *after* a surprise)
```

**The distinction from micromanagement:** Henry does not ask for reports on routine actions. The checkpoint appears specifically when the action is real, costly, or irreversible. Low-stakes actions remain fully delegated.

---

## Why This Pattern Matters

This pattern represents a calibrated answer to the question of where human judgment belongs in a highly automated system. Henry is not afraid of automation — he built an always-on Dream Cycle, a meeting-notes ingestion pipeline, and a multi-phase autopilot. But automation without human checkpoints at cost/risk boundaries produces surprise $30 bills, irreversible state mutations, and debugging loops that compound cost with each iteration.

The confirm-before-execute protocol is the safety layer that makes aggressive automation **sustainable**. Without it, the risk of an unexpected cost event or irreversible mistake grows as the automation becomes more capable. With it, the automation can be trusted to run autonomously in the 90% of cases that are routine, because the human has established that the 10% boundary cases will pause for review.

This pattern is closely related to — but distinct from — Henry's experiment-driven decision framework ([[wiki/personal/patterns/experiment-driven-decision-making]]): experiments should be run, but only after the cost and scope of the experiment have been explicitly understood and accepted.

---

## Connection to Adjacent Patterns

- **[[wiki/personal/patterns/idempotency-guard-automation-cost-control]]** — idempotency guards prevent runaway cost at the system level; the confirm-before-execute protocol prevents it at the human-agent interaction level. Together they cover both structural and behavioral cost controls.
- **[[wiki/personal/patterns/experiment-driven-decision-making]]** — every real execution is an experiment; the confirmation checkpoint is where Henry explicitly accepts the experiment's cost and scope before it runs.
- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — the "report first" half of the protocol is the diagnostic phase: the agent must demonstrate it has correctly understood the problem before being authorized to act on it.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — real tests surface multi-layer bugs; the confirmation checkpoint is Henry's way of managing the cost of each layer before committing to the next iteration.
- **[[wiki/personal/patterns/equanimity-under-cascading-failure]]** — equanimity about system bugs does not extend to unexpected cost. The confirm-before-execute protocol is how Henry channels potential impatience about cost surprises into a structural safeguard rather than ad hoc frustration.

---

## Related Pages

- [[wiki/personal/reflections/2026-05-15-dream-cycle-cost-shock-and-confirm-before-run-protocol-494dea]]
- [[wiki/personal/reflections/2026-05-15-debug-report-first-then-execute-892ed1]]
- [[wiki/personal/reflections/2026-05-14-dream-infra-verification-and-company-disclosure-12e3b1]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[wiki/personal/patterns/idempotency-guard-automation-cost-control]]
- [[wiki/personal/patterns/experiment-driven-decision-making]]
- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/equanimity-under-cascading-failure]]
