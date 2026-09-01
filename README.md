<div align="center">

# AS-OS Engineering

**A local-first, multi-agent operating system — built to keep working when the cloud can't.**

`v1.0.0` · verified against the live system, 2026-09-01

</div>

---

## Mission

Give one operator real leverage across real work — film production, publishing,
brand ventures — through a fleet of domain-specialized AI personas that act like a
small, trustworthy team, not a single black-box assistant.

Two things are non-negotiable in how that gets built:

1. **The system must never depend on the cloud to keep running.** Cloud LLMs are a
   legitimate, currently-necessary tool during this build phase — but the
   architecture has to keep local capability real and improving, not a fallback
   that only gets exercised when something breaks.
2. **The system must never fabricate.** A persona that doesn't know something, or
   whose tool failed, says so. A degraded answer is labeled degraded. Every
   verification gate in this system exists to make honesty the cheap default, not
   an aspiration.

## Vision — what "done" looks like

A fleet where **local and cloud work in a genuine, measured balance** — not
cloud-by-default-with-a-local-escape-hatch, and not a purist local-only system that
sacrifices real capability to prove a point. Concretely:

- Every persona's routing decision is backed by evidence (a judge score, a
  deterministic contract pass/fail), not a static assignment made once and never
  revisited.
- Local models handle real, non-trivial traffic by choice, on hardware sized for
  genuine on-GPU concurrency — not one model loaded at a time.
- Claude and other cloud models remain in the loop permanently, but as a
  deliberately-invoked tier for what they're actually best at, not the default path
  for everything.

## Where this actually stands — 2026-09-01

Verified live (`/health`, `version-check.sh`, direct config reads) — not a status
claim, a status *check*.

### Done

- **12-persona fleet, live.** `architect` (orchestrator), `oracle` (memory,
  local-only), `neo` (auditor, read-only), 6 domain personas, and a draft-only,
  human-approval-gated broadcasting tier — each with a disjoint tool set and a
  separate memory collection, no shared write surface.
- **3-tier LLM routing**, consolidated and gate-verified: Tier 1 (Claude —
  `architect`/`neo` only), Tier 2 (one gate-verified cheap cloud route for 7
  personas), Tier 2b (a Bangla-quality override for 2 more), Tier 3 (local Ollama,
  every persona's fallback, `oracle`'s only tier).
- **A deterministic verification pipeline** — an output-contract gate with no LLM
  self-grading, a narration guard that catches a tool call that was described but
  never executed, and an evidence bus that scores every completed task
  (faithfulness / relevancy / task-success / hallucination) without ever blocking
  the result.
- **Human-in-the-loop by construction.** Anything side-effecting waits for
  approval unless the operator's own work-mode setting says otherwise. Guardrails
  fail *open* on their own outage by explicit design — a safety check that can't
  run must not block legitimate work — because HITL, not the rail, is the actual
  backstop.
- **Confidence-gated local-first (Phase 0.5)** — live, currently seasoning. Six
  personas try their own local fallback first and only escalate to cloud on a real
  validation failure, visible per-run in the activity feed
  (`[local-first ✓]` / `[local-first failed → cloud]`).
- **The operator dashboard (`as-os-hq`)** unified into one window — chat, dispatch,
  memory, and customization behind a single URL — with Claude-style artifacts
  captured automatically from what personas produce.

### What "current hardware" actually is — an MVP, not the target

The single-GPU consumer hardware (GTX 970, 4GB VRAM) everything above runs on
is deliberately framed as an **MVP**: proof that the architecture works, on
the cheapest hardware that could prove it — not the system's baseline design
being incrementally patched. The real end-goal hardware is an **NVIDIA DGX
Spark** (128GB unified CPU/GPU memory, GB10 Grace Blackwell Superchip,
shipping since Oct 2025, $4,699 Founders Edition as of Feb 2026) — a
structurally different memory architecture that removes the current "one
model resident in VRAM" ceiling, trading it for a different bottleneck
(273 GB/s shared-memory bandwidth; independent benchmarking puts large-model
throughput ~4× slower than a datacenter GPU on the same workload — NVIDIA's
own marketing claims and that independent benchmark genuinely disagree, and
this document doesn't pick a side). A planned RTX 5060 Ti 16GB upgrade is an
**interim waypoint** on the way there, not itself the destination. The
operating principle: **on any GPU upgrade, the full system architecture
scales with it** — not just inference gets faster on an unchanged design.
Full detail: `as-os-sp/docs/HARDWARE_ROADMAP.md`.

### In progress / pending

- **Extending confidence-gated routing** from six personas to a genuine
  complexity-aware dispatcher — right now a persona either always tries local
  first or never does; the next real step is routing *individual requests* by
  actual difficulty, not by static persona assignment.
- **Local-model training / distillation** — proposed, not started. No training
  compute path is confirmed yet; the current GPU is inference-only capacity.
- **The interim GPU upgrade** (RTX 5060 Ti 16GB) — documented, not executed;
  the real end-goal (DGX Spark) is further out and not yet purchased either.
  Until either lands, one model resident in VRAM at a time is a hard ceiling
  on local concurrency, regardless of how good routing policy or local models
  get — see "What 'current hardware' actually is" above.
- **One unresolved routing gap** — a persona needing both real tool reliability
  and strong Bangla has no fully-verified route yet; the one route in the fleet
  verified for both is temporarily blocked by a third-party credit limit, expected
  to self-resolve.

### Explicitly unknown — not guessed at

- **No formal "fully operational" completion criteria exists yet.** No document
  in this system defines that numerically or by checklist, so no completion
  percentage is claimed here. This section will be replaced by a real one once
  that criteria is actually written down, not filled with an estimate.
- **Whether the GPU upgrade is a prerequisite milestone for the local-first
  roadmap, or an independent track**, hasn't been decided yet.

## How it's built — the engineering values behind the mission

- **Verification over trust, everywhere.** Every gate in this system is
  deterministic and reproducible where possible; an LLM judging its own output is
  used for *evidence*, never as the sole gate on whether something is "done."
- **Isolation as the default, not an afterthought.** Tool access, memory, and
  routing are scoped per persona from the start — role drift is prevented
  structurally, not by prompt instruction alone.
- **Measure, don't assume.** Every routing and caching decision in this system's
  history has been backed by a live test — a tool-calling gate run, a real
  end-to-end task through the actual queue — before being trusted, including
  reversing decisions the first measurement got wrong.
- **Honest about gaps.** A system that hides what it doesn't know yet is worse
  than one that states it plainly. This document is written to the same standard.

## Repositories

| Repo | What it is |
|---|---|
| **as-os-sp** | The brain — FastAPI + LangGraph, the persona fleet, memory, routing, and the verification pipeline |
| **as-os-hq** | The operator dashboard — Next.js, talking to the brain over a documented HTTP contract |
