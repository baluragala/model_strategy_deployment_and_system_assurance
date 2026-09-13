# GenAI-C8-W1-S2 — Model Strategy, Deployment & System Assurance
## Redesign spec: slide-grade deck + end-to-end notebook

Date: 2026-09-12
Status: approved

## Problem

The existing notebook (`GenAI_C8_W1S2_..._Zero_to_Hero.ipynb`, 49 cells) does not serve
the 150-minute live session it was written for.

1. **Nothing connects.** 39 markdown / 10 code cells. Routing (S5), guardrails (S16) and
   observability (S19) are independent vignettes. No object passes between sections. The
   ASCII diagrams describe a pipeline that never exists in code.
2. **No agenda mapping.** The agenda defines 6 blocks with fixed durations
   (30/25/30/20/20/25 min) and delivery modes. The notebook has 29 sections with no
   timings and no mode markers.
3. **Leaked generation artifacts.** `fileciteturn1file0L12-L28` and similar appear
   in 5 markdown cells.
4. **Fake computation.** `retrieval_hits=[1,1,0,1,1]` is a literal. `normalized()` scores
   a dict literal. Recall@5 is not computed because no retriever exists.
5. **Placeholder model names.** "Proprietary Frontier A/B", while the agenda's reading
   list names OpenAI, Anthropic, Gemini, Meta Llama, HuggingFace and Azure OpenAI.
6. **Capstone is a quiz.** Section 25 is 10 questions with no scaffolding, starter code
   or reference solution — unusable for a 25-minute guided block.
7. **No instructor layer.** Three of six blocks are Discussion / Guided Analysis. There
   are zero discussion prompts, timings or facilitator cues.

## Solution

One system, assembled incrementally across 9 stages. Each stage emits a real Python
object that the next stage consumes, and ends with an explicit `HANDOFF ->` cell that
prints the object being passed forward. Connection is demonstrated, not asserted.

### The 9-stage spine

| # | Stage | Emits |
|---|-------|-------|
| 1 | Workload specification | `WorkloadSpec` |
| 2 | Model class decision (proprietary vs open-weight) | `ModelClassDecision` |
| 3 | Model family shortlist | `ModelShortlist` |
| 4 | Router | `Router` |
| 5 | Deployment, capacity, latency, cost | `DeploymentPlan` |
| 6 | Orchestrator (retrieval + tools + model) | `Trace` |
| 7 | Evaluation harness | `EvalReport` |
| 8 | Red team + guardrails | `GuardedSystem` |
| 9 | Release gate, canary, lifecycle | `SystemManifest` |

### Agenda mapping (150 min, exact)

| Agenda block | Min | Mode | Stages |
|---|---|---|---|
| Decide model class strategically | 30 | Conceptual + Discussion | 1, 2 |
| Choose specific model families | 25 | Conceptual | 3 |
| Evaluate hosting feasibility | 30 | Conceptual + Guided Analysis | 4, 5 |
| Design evaluation pipelines | 20 | Conceptual | 6, 7 |
| Integrate robustness mechanisms | 20 | Conceptual | 8 |
| Apply end-to-end reasoning | 25 | Guided Analysis + Discussion | 9 + capstone |

### Key design decisions

- **Criteria weights are derived from `WorkloadSpec`, not hardcoded.** A tight p95 raises
  the latency weight; a Restricted data class raises the privacy weight. This is what
  makes Stage 2 genuinely downstream of Stage 1.
- **A real retriever** over a ~10-document synthetic KB, so Recall@K is measured.
- **A planted poisoned document** sits in that corpus. At Stage 6 it genuinely hijacks the
  agent's proposed tool call; Stage 8's guardrails are what stop it. Learners watch the
  system fail, then watch it hold.
- **Deterministic offline stub model** (no API keys, nothing to fail live in class), plus
  one optional real-provider adapter cell at the end.
- **Capstone flips one upstream constraint** (`data_class -> RESTRICTED`) and re-runs all
  9 stages. Routing, deployment, cost and the release gate all change. Reference
  solution included.

### Per-stage cell rhythm

```
### STAGE n - <name>            [Agenda block - N min]
  - Decision to make
  - Inputs (printed: the object Stage n-1 handed over)
  - Code: build it
  - Visual: see it
  - Try it: change one input, re-run
  - HANDOFF -> <Object> to Stage n+1 (printed)
```

## Deliverables

- `GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb` — rebuilt lab
- `deck/index.html` — reveal.js deck, ~70 slides, published as an Artifact
- `INSTRUCTOR_NOTES.md` — minute-by-minute run sheet

## Non-goals

- No live provider API calls in the core path.
- No GPU/vLLM benchmarking; capacity is first-order arithmetic with stated assumptions.
- No vendor benchmark claims; capability matrix is structural (modality, tool calling,
  licence, deployment surface) with a "verify against current docs" banner.
