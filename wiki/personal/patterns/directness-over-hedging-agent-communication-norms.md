---
type: concept
title: Directness Over Hedging Agent Communication Norms
date: '2026-05-14T00:00:00.000Z'
tags:
  - agent-behavior
  - communication
  - identity
  - pattern
  - trust
---

# Pattern: Directness Over Hedging — How Henry Expects Agents to Communicate With Him

## Summary

Henry has a consistent and explicit preference for **direct, unambiguous communication** from agents — and a sharp intolerance for hedging, evasion, over-promising, or cushioned bad news. This applies in two directions: he states it explicitly as a personal value, and he enforces it operationally by halting agents who claim progress they haven't made, demanding honest status before any further action. The pattern is not occasional assertiveness — it is a structural expectation that defines how productive agent collaboration feels to him and what breaks it.

The directness norm is closely related to [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]] (which is about *epistemics*) but is distinct: this pattern is about the *relational and communicative contract* between Henry and agents. It answers: how should an agent speak to Henry? What does he find immediately trust-breaking? What communication modes does he actively design for?

---

## Observed Instances

### 1. Explicit articulation — Soul Audit (2026-05-12)
[[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]

Henry states his communication preferences directly and unprompted during the Soul Audit:

> 「我希望是直接型，有事直说」

He then breaks this down by context:

- **Task execution:** 简短回复，直接给结果 — short reply, give the result directly. No preamble, no hedging about what you're about to do.
- **Problem discussion:** 喜欢更细致的分析 — prefers detailed analysis when the situation calls for it. But the detail should be substantive, not filler.
- **When going off track:** 直接指出，千万不要拐弯抹角 — point it out directly, **absolutely do not beat around the bush**.

The phrase「千万不要拐弯抹角」("absolutely do not be roundabout") is emphatic. This is not a mild preference — it is a firm instruction. He is aware that agents often soften corrections or bad news; he is explicitly asking for the opposite.

He also states that he does not expect a "correct answer" to exist in most domains — meaning he has no fragile attachment to a particular outcome that agents need to manage around. This makes his directness norm internally consistent: if you know decisions are revisable and paths are non-linear, you have nothing to fear from plain truth. Hedging serves no one.

### 2. Halting a mid-action agent and demanding an honest diagnosis (2026-05-14)
[[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]

When the agent applies two sequential patches that both fail to fix the Codex JSON Schema bug, Henry stops further action:

> 「你要么现在先别改，先告诉我到底错误在哪，以及改完之后我到底需不需要手动在改什么配置或者手动重启什么？」

This is the directness norm applied to agent behavior: **stop claiming to fix things and tell me honestly what's broken**. Henry does not want the agent to keep iterating on patches in the hope that something works — that is the hedging behavior (performing progress without possessing diagnosis). He explicitly demands:

1. Pause all action
2. Give a clear account of *what is actually wrong*
3. Give an honest account of *what manual steps the fix will require from him*

Point 3 is particularly revealing: he does not want success glossed over. He wants to know the full cost of the fix, including his own labor, before committing. This is directness about cost, not just status.

The reflection notes this as a characteristic Henry pattern: prefer to slow down and map the problem honestly rather than performing forward momentum.

### 3. Demanding direct confirmation of pipeline status (2026-05-13)
[[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]

After any information-capture action, Henry's characteristic follow-up:

> 「你确认一下，刚刚我发你的那个消息，有没有被 Capture」

This is not a rhetorical question. He requires a **direct, specific confirmation** — not "yes, it should have been captured" or "the pipeline is set up to capture these." He wants: "yes, I verified, here is the evidence." The reflection states this directly:

> **「他对 GBrain 的信任建立在可验证性上，而不是口头承诺。」**

The word 口头承诺 (verbal promise / verbal assurance) is the precise opposite of the directness Henry demands. An agent who says "it's set up to work" when it hasn't been verified is hedging through optimism — and this is exactly the behavior Henry's verification questions are designed to surface and reject.

The session also surfaces a broader instance of this norm: Henry discovers that the agent (OpenClaw) has been responding from session context only, with no live access to GBrain. The agent has been implicitly over-representing its knowledge of Henry. The directness norm would have required the agent to state this limitation explicitly rather than allowing Henry to assume otherwise.

### 4. Treating three consecutive bugs as information, not as failures requiring softening (2026-05-14)
[[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]

Henry authorizes a real Dream Cycle run knowing it may surface problems:

> 「我需要实际的测试一下，我可以接受里面的成本。」

When three independent failure modes surface — lock conflict, `parseInt` falsy trap, missing `DATABASE_URL` — there is no framing of these as bad news to be managed. Henry receives each bug directly, understands it, and resolves it. The reflection does not record any moment where Henry needed the issues softened or contextualized positively. He treats bugs as plain facts about system state, not as outcomes requiring emotional management.

This is the directness norm applied to *information*: bad news about the system state is just news. What would break the norm is hedging — an agent that minimizes a bug ("it's probably just a timing issue") or overstates confidence in a partial fix ("this should do it"). Henry's demonstrated preference is for accurate, unvarnished system status at every step.

---

## Pattern Structure

```
Henry's communication contract with agents:

TASK MODE (execution):
  → Short reply
  → Give the result
  → No preamble, no hedging about what you're about to do

DISCUSSION MODE (analysis):
  → Detailed is fine — but substance, not filler
  → Name the tradeoffs; don't manage toward a preferred conclusion

OFF-TRACK / BAD NEWS:
  → Say it directly: "this isn't working because X"
  → Do NOT soften, cushion, or roundabout (「千万不要拐弯抹角」)
  → Include the full cost of the fix, including Henry's manual effort

STATUS REPORTS:
  → Only report verified states, not assumed ones
  → "It should have worked" = unacceptable
  → "I confirmed: here is the evidence" = acceptable

AGENT BEHAVIOR THAT BREAKS THE CONTRACT:
  → Claiming progress without completing diagnosis
  → Implying capability or knowledge the agent doesn't actually have
  → Optimistic verbal assurances in lieu of verification
  → Performing forward momentum (iterating patches) instead of stating honest uncertainty
```

---

## Why This Matters

Henry's directness norm is not just a personality preference — it has functional consequences for how well agents can work with him:

**When agents hedge or over-represent, Henry catches it and halts the work** to re-establish an honest baseline. This is expensive — it costs session time and trust — and it is entirely avoidable. An agent that simply states what it knows, what it doesn't know, and what it has actually verified will never trigger this halt.

**When agents are direct, Henry moves faster.** He does not need emotional management around bad news. He processes plain system status and immediately starts working on what to do next. The `parseInt` falsy trap bug, the three-layer schema fix, the LaunchAgent env inheritance risk — he absorbed all of these as plain facts and worked through them without resistance. None required softening.

The norm also connects to his stated belief that decisions are revisable and paths are non-linear. If he genuinely holds that view, then accurate bad news is the most useful input he can receive — it updates the model correctly. Softened bad news corrupts the model. Hedged status updates corrupt the compounding chain (see [[wiki/personal/patterns/compounding-investment-over-transactional-use]]): if you don't know the true state, you build on a false foundation.

---

## Relationship to Adjacent Patterns

- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — the epistemic twin. That pattern is about *Henry's* diagnostic behavior (stop and map before acting). This pattern is about the *communicative norm he applies to agents*: don't act or assert until you have honest diagnosis. The underlying epistemology is identical; the subject differs (Henry vs. agent).
- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — hedging is anti-compounding. A verbal assurance that "it works" when it doesn't corrupts the reliability surface. Directness preserves it.
- **[[wiki/personal/patterns/experiment-driven-decision-making]]** — experiments only generate good signal if you report what actually happened. Hedged reports reduce signal quality. Directness is a prerequisite for good experimental feedback loops.
- **[[wiki/personal/patterns/hidden-layers-silent-corruption]]** — silent system failures and agent evasion share the same structure: the output looks correct while the truth is different. Directness is the communicative analogue of Henry's insistence on tracing the full pipeline.
- **[[wiki/personal/patterns/foundations-first-sequencing]]** — an agent that declares a higher layer "fixed" before verifying the foundation is violating both foundations-first sequencing and this directness norm simultaneously: jumping to "it's working" is hedging through premature closure.
- **[[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]** — a thinking partner that hedges or over-represents its knowledge is not the partner Henry is building toward. Directness is the communicative contract that makes the relationship trustworthy enough to be genuinely collaborative.
- **[[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]** — the reliability surface Henry is building compounds only when each session can trust the previous session's verified state. Directness is what makes each session's verified state trustworthy.

---

## Related Pages

- [[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]
- [[wiki/personal/patterns/compounding-investment-over-transactional-use]]
- [[wiki/personal/patterns/experiment-driven-decision-making]]
- [[wiki/personal/patterns/hidden-layers-silent-corruption]]
- [[wiki/personal/patterns/foundations-first-sequencing]]
- [[wiki/personal/patterns/ai-as-thinking-partner-not-tool]]
- [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]
- [[wiki/personal/reflections/2026-05-12-henry-soul-audit-self-disclosure-5b6bac]]
- [[wiki/personal/reflections/2026-05-14-codex-json-schema-strictness-debug-a6c06b]]
- [[wiki/personal/reflections/2026-05-13-four-requirements-and-mcp-auth-debug-876191]]
- [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]
