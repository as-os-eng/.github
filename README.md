<div align="center">

# AS-OS Engineering

**Building a local-first, multi-agent operating system.**

</div>

---

AS-OS is a self-hosted agentic AI system: a 12-persona fleet, each domain-isolated
(its own tools, its own memory), coordinated through an explicit delegation and
verification pipeline, and designed to run on consumer hardware rather than depend
on the cloud by default.

## Repositories

| Repo | What it is |
|---|---|
| **as-os-sp** | The brain — FastAPI + LangGraph, the 12-persona fleet, memory, tool routing, and the verification pipeline |
| **as-os-hq** | The operator dashboard — Next.js, talking to the brain over a documented HTTP contract |

## How it's built

- **Local-first, not local-only.** Cloud LLMs do real work where they earn it; the
  system is designed so local models can too, and to keep running when the cloud
  can't be reached.
- **Verification over trust.** Deterministic output checks, a narration guard, and
  an evidence-scoring pass on completed work — not an LLM grading its own homework.
- **Human-in-the-loop by construction.** Anything with a real side effect waits for
  approval by default.

<!-- Optional — fill in and uncomment whichever you actually want public:
[![Website](https://img.shields.io/badge/Website-000?style=for-the-badge)](#)
-->
