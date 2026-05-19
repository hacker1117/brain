---
type: concept
title: >-
  Pattern: Field Research Before Training Delivery — Interview the Audience
  Before You Build the Slide
date: '2026-05-18T00:00:00.000Z'
tags:
  - client-work
  - consulting
  - field-research
  - pattern
  - training
---

# Pattern: Field Research Before Training Delivery — Interview the Audience Before You Build the Slide

## Summary

Henry does not arrive at a training engagement with a polished generic deck and deliver it. Before any training session, he conducts **on-site, role-specific interviews with the actual people who will be in the room** — to understand their real workflows, daily pain points, and existing tool use. Only after that research does he shape the training content. The declared motivation is consistent: to avoid saying 「你们在网上随便都能听到的」 (things anyone can hear online). Field research is what makes training specific, credible, and useful rather than generic and forgettable.

This pattern also functions as a **trust-building mechanism**: arriving to interview rather than to lecture signals that Henry takes the audience's reality seriously, not just his own content.

---

## Observed Instances

### 1. GWC Production Manager pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-production-manager-ai-needs-interview-044aed]]

Henry goes on-site at GWC (固丝德夫沃夫钢绳(苏州)有限公司) before the 2026-05-17 training and interviews the production manager one-on-one. The explicit framing:

> 「目的是避免讲「你们在网上随便都能听到的」内容。」

From this interview he learns: despite being a foreign-invested factory, GWC still uses paper timesheets; Excel + WeChat are the primary digital infrastructure; the production daily report takes approximately two hours to make manually. None of this would have been apparent from a generic manufacturing-sector profile.

### 2. GWC Warehouse Clerk pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-warehouse-clerk-reconciliation-pain-interview-4f56d0]]

Henry interviews the warehouse clerk and surfaces a specific, invisible pain: the cutting system's export format is incompatible with the downstream Excel template, so the clerk manually copy-pastes data for every export shipment. This is not a "digital transformation" problem visible from the outside — it is a format-mismatch problem that only emerges from watching someone do the job.

### 3. GWC EHS Worker pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-ehs-worker-ai-needs-interview-547b48]]

Henry interviews the Environmental Health & Safety worker and discovers: (a) the inspection rounds cannot be replaced by AI because they require physically seeing the equipment, and (b) the worker is already self-taught on 豆包 (Doubao) for document generation — a fact that changes the training calibration entirely. Without the interview, Henry might have introduced Doubao as a discovery; instead he can build on an existing skill.

### 4. GWC Production Foreman pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-production-foreman-daily-workflow-interview-6682e9]]

Henry interviews a production foreman and discovers that collecting and aggregating the 30–40 shift output sheets across machine bays takes over one hour per day — an entirely manual reconciliation task. He also learns that the foreman sees cross-department exceptions as something 「没办法，只有靠人去处理」 — calibrating which problems are AI-tractable.

### 5. GWC Equipment Maintenance Head pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-equipment-maintenance-ai-needs-interview-8f14f4]]

Henry interviews the maintenance supervisor and discovers the fault report form workflow: paper form → manual system entry → data tracking via ERP. The core friction is the transcription step, not the form itself. Without the interview, a training designed around "using AI to write reports" would miss that the bottleneck is data re-entry, not report authorship.

### 6. GWC Rope Slitting Planner pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-rope-slitting-planner-ai-needs-interview-11450f]]

Interview with the cutting/slitting plan coordinator surfaces a cascade of operational failures: urgent order insertions disrupting planned runs, inventory inaccuracies from legacy systems, and a planning system rollout that failed because the real-world exceptions it couldn't handle were too frequent. These are systemic problems masquerading as individual pain points — only visible through direct conversation.

### 7. GWC Warehouse Supervisor pre-training interview (2026-05-11)
[[wiki/personal/reflections/2026-05-11-gwc-warehouse-supervisor-ai-needs-interview-da4ab2]]

Henry interviews the warehouse supervisor and finds that the highest-friction part of the job is cross-department negotiation — the supervisor's primary frustration is 「和人员沟通」 (communicating with people), specifically the self-protective behavior that causes other departments to push work back. This challenges the assumption that workflow digitization is the primary need; the supervisor's real need is communication scaffolding.

### 8. GWC Training Course Structure Design informed by research (2026-05-14)
[[wiki/personal/reflections/2026-05-14-gwc-training-course-structure-planning-878953]]

When structuring the GWC training (morning theory + afternoon hands-on), Henry explicitly integrates the field research findings: the audience has low digital literacy, some have never used a computer for work, and the relevant tools must be voice-first or image-recognition-based — not text-heavy. The training structure is a direct output of the interview research, not a default template.

The field research also forced a difficult tool-selection decision: which AI tool to use for the hands-on portion. The low-literacy, older manufacturing audience needed something that avoided text-heavy interfaces, which ruled out many otherwise capable tools.

### 9. GWC Training Day Debrief — field research strategy validated (2026-05-18)
[[wiki/personal/reflections/2026-05-18-gwc-training-day-debrief-1dd4f1]]

The post-training debrief confirms that the field research strategy paid off. The audience profile established during the pre-training interviews — low digital literacy, 40–50 age range, limited AI exposure — directly justified the high-practice-ratio design (dramatically increased hands-on time vs. lecture). The training landed well because the design was calibrated to the actual audience, not an assumed one.

This is the feedback-loop closure of the field research pattern: research shapes design, design shapes delivery, delivery results validate the research-first investment.

---

## Pattern Structure

```
New training engagement confirmed
  → Before building any content: go on-site (or conduct structured calls)
  → Interview each major role or function separately
  → Discover: real daily workflows, actual tools in use, specific friction points,
    existing AI/digital literacy, and which problems are NOT AI-tractable
  → Shape training content around the specific findings:
      - Build on tools the audience already uses
      - Target the actual bottleneck, not the assumed one
      - Calibrate AI capability claims against physical/process constraints
  → Deliver training that is recognizably about their work, not generic
  → Debrief: does the outcome validate the research-shaped design?
```

**What this avoids:**
- Training sessions where participants feel the content is generic ("could apply to any company")
- Overfitting to a single informant's view (hence: multiple roles, multiple interviews)
- Introducing tools the audience already uses as if they were discoveries
- Proposing AI solutions for problems that require human judgment or physical presence

**What it enables:**
- Specific, credible content that references the audience's actual tools and friction
- Trust established before the room is even assembled
- Calibrated recommendations (AI-tractable vs. not) that survive pushback
- A closed feedback loop: research → design → delivery → debrief → next engagement

---

## Connection to Adjacent Patterns

- **[[wiki/personal/patterns/experiment-driven-decision-making]]** — field interviews are information-gathering experiments: each one updates the model of the audience before any commitment to content is made.
- **[[wiki/personal/patterns/diagnose-before-act-verifiability-trust]]** — interviews are the diagnostic phase of training design; they prevent acting (building slides) on assumed rather than verified audience needs.
- **[[wiki/personal/patterns/compounding-investment-over-transactional-use]]** — the field research investment compounds: a training session that resonates generates referrals, repeat engagements, and channel partner trust — all of which have higher multiplier value than a one-off generic session.
- **[[wiki/personal/patterns/trust-first-dual-layer-engagement]]** — field research is the operational implementation of the trust-first layer of any engagement: conducting interviews before building content signals that Henry takes the audience's reality seriously before the session begins.

---

## Related Pages

- [[wiki/personal/reflections/2026-05-11-gwc-production-manager-ai-needs-interview-044aed]]
- [[wiki/personal/reflections/2026-05-11-gwc-warehouse-clerk-reconciliation-pain-interview-4f56d0]]
- [[wiki/personal/reflections/2026-05-11-gwc-ehs-worker-ai-needs-interview-547b48]]
- [[wiki/personal/reflections/2026-05-11-gwc-production-foreman-daily-workflow-interview-6682e9]]
- [[wiki/personal/reflections/2026-05-11-gwc-equipment-maintenance-ai-needs-interview-8f14f4]]
- [[wiki/personal/reflections/2026-05-11-gwc-rope-slitting-planner-ai-needs-interview-11450f]]
- [[wiki/personal/reflections/2026-05-11-gwc-warehouse-supervisor-ai-needs-interview-da4ab2]]
- [[wiki/personal/reflections/2026-05-14-gwc-training-course-structure-planning-878953]]
- [[wiki/personal/reflections/2026-05-18-gwc-training-day-debrief-1dd4f1]]
- [[wiki/personal/patterns/trust-first-dual-layer-engagement]]
