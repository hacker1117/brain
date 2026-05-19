---
type: concept
title: Experiment Driven Decision Making
date: '2026-05-14T00:00:00.000Z'
tags:
  - decision-making
  - experimentation
  - identity
  - pattern
  - strategy
---

# Pattern: Experiment-Driven Decision Making — No Right Answer, Only Current-Information Optima

## Summary

Henry does not look for correct answers to strategic decisions. He looks for **well-designed experiments** that generate the best information with the least irreversible cost. His explicit belief is that most AI and business decisions have no single right answer — only paths that are more or less informed. Every action is therefore framed as a probe: what will we learn? What do we now know that we didn't? Decisions are expected to be revised; paths are expected to be non-linear. This is not a coping mechanism for uncertainty — it is Henry's deliberate epistemology for navigating complex, multi-variable domains.

---

## Observed Instances

### 1. Explicit philosophy articulation — Soul Audit (2026-05-12)
[[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]

Henry states his decision framework directly during the Soul Audit:

> 「我希望所有事情都是有对比和选择的，我也认为很多 AI 方向的事情和商业上的事情并不会有一个'正确答案'，所以我希望是通过设计各种小的实验和尝试的动作，来收集信息，我们一起根据信息再来分析和最终决策的。我认为任何决策都是可变的，而且最终的路径都是曲折的，但我们每做一步都要要获得有用的信息和数据」

Key structural properties he names explicitly:
- **Always have a comparison set** (「我希望所有事情都是有对比和选择的」) — he doesn't evaluate one option in isolation; options must be compared
- **No correct answer** — especially in AI and business domains, the answer is underdetermined; humility is baked into the process
- **Small experiments over large bets** — design the smallest action that yields useful signal
- **Decisions are revisable** — commitment is provisional; it holds until better data arrives
- **Every step must produce information** — an action that teaches you nothing is a wasted step, even if it succeeds

This is not a vague preference for flexibility — it is a fully articulated methodology that Henry applies consistently.

### 2. Re-sequencing four requirements based on information value (2026-05-13)
[[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]

When Henry arrives with four goals for the session, he immediately accepts a re-ordering proposed by the agent — not because he lacks opinions, but because the agent correctly identifies which goal, when done first, generates the highest-value information for all subsequent decisions:

> 「先把「存进去的东西怎么被用上」搞清楚，再大量存数据才有意义。」

Henry doesn't push back. He recognizes that doing Need 2 (how agents use GBrain) before Need 1 (ingest more data) is the experiment-first logic: understand the retrieval mechanism before you can evaluate whether more data inputs are working. Investing heavily in data capture before retrieval is tested is a bet with no learning loop — exactly what the framework rejects.

He also *defers* Need 3 (Telegram group multi-topic) to last, again consistent with the framework: the most complex, most uncertain goal gets attempted only after simpler ones have generated relevant signal.

### 3. Authorizing a real Dream Cycle test — accept cost, generate real signal (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

Henry explicitly frames the Dream Cycle run as a deliberate experiment, not a production deployment:

> 「你现在来手动跑一遍完整的Dream流程吧。我需要实际的测试一下，我可以接受里面的成本。」

The phrase「我可以接受里面的成本」is the experiment-driven logic in action: he is trading a known, bounded cost (API spend) for high-quality information about whether the full pipeline works in the real world. A simulated test would generate lower-quality signal — it wouldn't expose the actual failure modes. He accepts the cost because the information return justifies it.

The session yields three bugs (lock conflict, `parseInt` falsy trap, missing `DATABASE_URL`) — all real failure modes that a "safe" test would have missed. This is the experiment-driven framework working as designed: the small, real-cost probe surfaces what nothing cheaper could.

### 4. Switching models as a probe with immediate diagnostic follow-through (2026-05-14)
[[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]

Henry switches OpenClaw's underlying model from Claude to OpenAI Codex (gpt-5.5). This is not a permanent migration decision — it is a probe. He wants to know how the system behaves with a different model. The immediate JSON Schema rejection error is, from an experiment-driven perspective, valuable output: it reveals that GBrain's schema definitions were Claude-tolerance-dependent and would fail against any stricter consumer.

Rather than treating this as a setback, Henry drives through the three-layer debugging process to get Codex working — because the information value of having a model-agnostic schema is high. The experiment revealed a latent fragility; fixing it is how you close the learning loop.

---

## Pattern Structure

```
Strategic question or decision point
  → Reject: "what is the right answer?"
  → Ask instead: "what experiment would give us the best signal?"
  → Design the smallest, most reversible action that:
      (a) compares at least two options, OR
      (b) yields information that changes subsequent decisions
  → Execute with real conditions where possible (low-quality signal is not signal)
  → Capture what was learned
  → Revise the decision based on data
  → Repeat: the path is expected to be non-linear
```

**What he consistently avoids:**
- Making large, hard-to-reverse decisions without comparison options
- Committing to a direction before running any confirming probe
- Accepting synthetic or simulated tests when real conditions are accessible
- Treating a decision as final ("any decision is revisable")
- Actions that succeed or fail but teach nothing

---

## Connection to Technical Patterns

The experiment-driven framework is not confined to strategic decisions — it shapes Henry's technical behavior too:

**Consumer switches as experiments:** Switching from Claude to Codex, from SSE to streamable-HTTP, from autopilot-only to manual trigger — each is a deliberate or incidental probe that generates information about the system's true state. See [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]: stricter consumers are the first honest reporters of latent design debt. Henry treats their failures as signal, not setback.

**First real tests as highest-quality experiments:** Simulated tests are permissive consumers; real-condition tests surface what nothing cheaper could. The experiment-driven framework explains why Henry insists on real-cost, real-condition runs — they are the experiments with the highest information yield. See [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]].

**Hidden layers as confounders:** An experiment whose result is "the fix should have worked" is not an experiment — the causal chain was interrupted by an intermediate layer that silently dropped the change. Tracing hidden layers is what closes the experimental feedback loop. See [[wiki/personal/patterns/hidden-layers-silent-corruption]].

**Configuration multiplicity as a source of experimental noise:** If the same config value exists in multiple representations and you changed only one, your "experiment" (did this fix work?) is confounded by which representation the consumer is reading from. See [[wiki/personal/patterns/configuration-multiplicity-hazard]].

**Automation as the mechanism for continuous experimentation:** Manual activation breaks the experiment-driven loop by making runs infrequent and inconsistent. Automating pipelines (Dream Cycle autopilot, meeting capture, auto-retrieval) is what enables continuous, reliable signal accumulation. See [[wiki/personal/patterns/automation-first-reduce-manual-activation]].

**GBrain synthesis as the learning capture mechanism:** The Dream Cycle's pattern pages are what convert experiment outputs (reflections, debugging sessions) into reusable intelligence. Without synthesis, experiments generate data but not knowledge. See [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]].

**The thinking-partner model as the end state:** A well-functioning experiment-driven process requires a collaborator who can reason about the experiments, surface relevant past data, and help design the next probe. This is exactly the "thinking partner" role Henry is building GBrain and OpenClaw toward. See [[wiki/personal/patterns/ai-as-thinking-partner-not-tool]].

---

## Relationship to Adjacent Patterns

- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — that pattern is about epistemics at the *debugging* level: stop before acting, map the problem, verify outcomes. This pattern is about *strategic* decision-making: how to choose between options, how to sequence uncertainty. The underlying impulse is the same (evidence > assertion) but the domain and scale differ.
- **[[wiki/personal/patterns/foundations-first-sequencing]]** — that pattern is about ordering work correctly (fix the base layer first). This pattern explains *why* re-sequencing is so natural for Henry: he is always looking for the action that generates the most useful information, and that action is usually the most foundational one.
- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — experiments that generate high-quality information *compound*: each one makes the next decision cheaper and better-informed. The experiment-driven framework is how compounding gets operationalized at the decision level.
- **[[wiki/personal/patterns/directness-over-hedging-agent-communication-norms]]** — experiments only generate clean signal if outcomes are reported honestly. Hedged reports corrupt the feedback loop; directness is a prerequisite for the experiment-driven framework to function.
- **[[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]** — consumer switches are a class of experiment: switching from a permissive to a strict consumer generates information that the tolerant consumer was suppressing. Henry's response (fix to the strictest standard) closes the learning loop permanently.
- **[[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]** — real-condition tests are the highest-fidelity experiments. Their multi-layer output is not surprising to Henry — it is the expected yield of running a genuine probe rather than a simulated one.
- **[[wiki/personal/patterns/hidden-layers-silent-corruption]]** — hidden layers are confounders in the experimental chain. Tracing them is what ensures the experiment's result accurately reflects the actual system state, not an artifact of pipeline silencing.
- **[[wiki/personal/patterns/configuration-multiplicity-hazard]]** — multiplicity introduces noise into experiment results: "it didn't work" may mean "the fix wasn't correct" or "the fix didn't reach the consumer." Resolving multiplicity is a prerequisite for clean experimental signal.
- **[[wiki/personal/patterns/automation-first-reduce-manual-activation]]** — automation converts episodic experiments into continuous signal accumulation. Manual activation makes experiments infrequent; automation makes them structural.
- **[[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]** — the thinking-partner model is the end state that the experiment-driven process is building toward: a collaborator who reasons with Henry about what the experiments mean and what the next probe should be.
- **[[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]** — GBrain is both the subject of Henry's infrastructure experiments and the instrument that captures their outputs (via Dream Cycle synthesis into pattern pages).

---

## Related Pages

- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/foundations-first-sequencing]]
- [[wiki/personal/patterns/compounding-investment-over-transactional-use]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
- [[wiki/personal/patterns/directness-over-hedging-agent-communication-norms]]
- [[wiki/personal/patterns/consumer-dependent-behavior-tolerance-variance]]
- [[wiki/personal/patterns/first-real-test-surfaces-multiple-layers]]
- [[wiki/personal/patterns/hidden-layers-silent-corruption]]
- [[wiki/personal/patterns/configuration-multiplicity-hazard]]
- [[wiki/personal/patterns/automation-first-reduce-manual-activation]]
- [[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]
- [[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]
- [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
- [[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]
