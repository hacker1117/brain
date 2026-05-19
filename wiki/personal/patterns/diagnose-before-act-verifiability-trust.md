---
type: concept
title: Diagnose Before Act Verifiability Trust
date: '2026-05-14T00:00:00.000Z'
tags:
  - debugging
  - decision-making
  - pattern
  - trust
  - verification
---

# Pattern: Diagnose Before Acting — Trust Built on Verifiability

## Summary

Henry consistently pauses execution when a situation is unclear, demanding an explicit diagnosis — a map of the problem space — before allowing any further action. His trust in agents, systems, and decisions is grounded in verifiability: he does not accept verbal assurances or "it should work" conclusions. Every step must produce inspectable evidence.

---

## Observed Instances

### 1. Authorizing a real Dream Cycle run and debugging the result (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

Henry doesn't just ask "did the Dream Cycle run?" — he explicitly authorizes a live test with real cost, treating it as a deliberate diagnostic probe:

> 「你现在来手动跑一遍完整的Dream流程吧。我需要实际的测试一下，我可以接受里面的成本。」

When obstacles appear, he works through each one systematically rather than declaring partial success. The `parseInt("0") || 12` bug — where setting `cooldown_hours=0` silently reverted to 12h — was particularly telling: the system appeared to accept the setting but didn't apply it. The fix (`-1` with `Math.max(0, val)`) required understanding the *actual behavior*, not the *intended behavior*. No verbal confirmation was accepted; only a completed cycle with 6 written pages constituted proof.

### 2. Multi-layer debugging of JSON Schema bug (2026-05-14)
[[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]

After the agent twice claimed to have fixed the Codex schema rejection error — and was wrong both times — Henry intervened:

> 「你要么现在先别改，先告诉我到底错误在哪，以及改完之后我到底需不需要手动在改什么配置或者手动重启什么？」

He stopped the agent mid-action, demanded a system-level diagnosis (what is actually broken, what are the side effects of a fix), and only then allowed further changes. The reflection explicitly names this Henry's pattern: prefer to slow down and map the problem boundary rather than iterate blindly.

Notably, his question explicitly asks about manual restart steps — a direct application of the [[wiki/personal/patterns/configuration-multiplicity-hazard]] insight: fixing the source file is not the same as the consumer receiving the fix.

### 3. GBrain capture verification habit (2026-05-13)
[[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]

Henry's consistent question after any information-capture action:

> 「你确认一下，刚刚我发你的那个消息，有没有被 Capture」

He does not assume the pipeline worked. He requires a verification step before moving on. The reflection states directly: **「他对 GBrain 的信任建立在可验证性上，而不是口头承诺。」** (His trust in GBrain is built on verifiability, not verbal promises.)

### 4. Experiment-driven decision framework (2026-05-12)
[[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]

Henry's stated philosophy:

> 「我希望所有事情都是有对比和选择的……所以我希望是通过设计各种小的实验和尝试的动作，来收集信息，我们一起根据信息再来分析和最终决策的。我认为任何决策都是可变的，而且最终的路径都是曲折的，但我们每做一步都要要获得有用的信息和数据」

There is no single "correct answer" — there is only "the best option given current information." Every step is treated as a diagnostic probe: what did we learn? What do we now know that we didn't before? This is the same verifiability impulse applied to strategic decisions, not just debugging.

---

## Pattern Structure

```
Problem or uncertainty arises
  → STOP: do not act until the problem boundary is clear
  → Diagnose: map root cause, side effects, dependencies
  → Design a small verifiable action
  → Execute
  → CHECK: did it actually work? Produce evidence — at the consumer, not the source
  → Only then: proceed or revise
```

This loop applies equally to:
- Technical debugging (multi-layer system faults, silent config mis-application)
- Agent behavior (did capture really happen?)
- Business and AI decisions (small experiments over big bets)

The "verify at the consumer, not the source" corollary is especially important for systems where configuration exists in multiple representations — see [[wiki/personal/patterns/configuration-multiplicity-hazard]]. Henry's question about manual restart steps is the operational expression of this: fixing the source is not verification; the consumer running correctly on the new config is verification.

---

## Why This Matters

Henry's impatience surfaces not with slowness per se, but with **unverifiable progress** — agents who say "done" when nothing has actually changed, or decisions made without a comparison baseline. The pattern reflects a deeper epistemology: **knowledge requires evidence, not assertion**. This makes him a demanding but reliable collaborator — he will catch false positives and won't proceed on vibes.

A particularly sharp variant: systems that *appear* to accept a configuration change but silently revert it (like the `parseInt` falsy trap) or route it to the wrong target (wrong `DATABASE_URL`) are especially intolerable to him, because they break the evidential loop entirely.

For the compounding motivation behind this rigor, see [[wiki/personal/patterns/compounding-investment-over-transactional-use]]: a false "it works" doesn't just waste one session — it corrupts the reliability surface that future sessions build on.

The verification discipline also has a strong integration dimension: Henry does not verify components in isolation — he verifies the *whole pipeline* at the consumer end. This is the diagnostic expression of [[wiki/personal/patterns/integration-over-specialization-design-philosophy]]: a component that passes its own test but fails in the integrated system is not verified. Henry's insistence on end-to-end evidence (6 pages embedded, Codex successfully calling the tool, capture confirmed live) reflects this integration standard.

---

## Relationship to Adjacent Patterns

- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — *why* verifiability matters: an unverifiable "success" corrupts the compounding chain. Each session's reliability surface is what future sessions build on.
- **[[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]** — the GBrain stack is exactly the environment where this pattern is most tested: complex, multi-layer, with many components that must each be verified independently.
- **[[wiki/personal/patterns/foundations-first-sequencing]]** — the sequencing logic and the verification logic are complementary: fix the base layer first, then verify it actually propagated before building the next layer up.
- **[[wiki/personal/patterns/configuration-multiplicity-hazard]]** — a specific class of "it didn't take effect" failures that this pattern's verification discipline catches. Henry's demand for consumer-level verification rather than source-level verification is the direct response to multiplicity: the source being correct is a necessary but not sufficient condition.
- **[[wiki/personal/patterns/hidden-layers-silent-corruption]]** — hidden layers are the structural explanation for *why* source-level verification fails. The diagnostic discipline here is the behavioral response: stop and trace the full pipeline, don't accept the source check as proof.
- **[[wiki/personal/patterns/directness-over-hedging-agent-communication-norms]]** — the communicative twin. This pattern is about Henry's epistemic behavior; directness is about the communicative norm he demands of agents. Both converge on the same rejection of unverifiable claims.
- **[[wiki/personal/patterns/experiment-driven-decision-making]]** — experiments only generate honest signal if outcomes are verified. An experiment whose result is "should have worked" is not an experiment.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — real tests are the verification mechanism at the system level. Henry's insistence on real-cost, real-condition tests is the large-scale version of the "verify at the consumer" principle.
- **[[wiki/personal/patterns/integration-over-specialization-design-philosophy]]** — Henry verifies at the *integrated pipeline level*, not the component level. End-to-end evidence (cycle ran to completion, all consumers received the fix) is the only valid verification standard for a system designed to be fully connected.

---

## Related Pages

- [[wiki/personal/patterns/compounding-investment-over-transactional-use]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
- [[wiki/personal/patterns/foundations-first-sequencing]]
- [[wiki/personal/patterns/configuration-multiplicity-hazard]]
- [[wiki/personal/patterns/hidden-layers-silent-corruption]]
- [[wiki/personal/patterns/directness-over-hedging-agent-communication-norms]]
- [[wiki/personal/patterns/experiment-driven-decision-making]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/integration-over-specialization-design-philosophy]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]
- [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]
- [[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]
