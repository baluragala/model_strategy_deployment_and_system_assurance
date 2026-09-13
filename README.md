# Model Strategy, Deployment & System Assurance

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/baluragala/model_strategy_deployment_and_system_assurance/blob/main/GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb)

**GenAI-C8 · Week 1 · Session 2** — a hands-on lab that builds an Enterprise IT Support Copilot end
to end across nine connected architecture stages.

Most architecture material shows you a diagram of a pipeline. This notebook builds one. Each stage
makes one decision, produces one real Python object, and hands that object to the next stage.
Change the workload in Stage 1 and the model choice, routing, infrastructure bill and release
verdict all change with it.

## The nine stages

| # | Stage | Emits |
|---|-------|-------|
| 1 | Workload specification | `WorkloadSpec` |
| 2 | Model class decision — proprietary vs open-weight | `ModelClassDecision` |
| 3 | Model family shortlist | `ModelShortlist` |
| 4 | Router — policy first, cost last | `Router` |
| 5 | Deployment: capacity, latency, cost | `DeploymentPlan` |
| 6 | Orchestrator — retrieval + tools + model | `Trace` |
| 7 | Evaluation harness | `EvalReport` |
| 8 | Red team + guardrails | `GuardedSystem` |
| 9 | Release gate, canary, lifecycle | `SystemManifest` |

The capstone flips **one boolean** (`sovereign_only=True`) and re-runs all nine stages. Eight of
fourteen tracked properties change — the model class flips, the reasoning model is replaced, the
private route dissolves, the bill changes, and the latency failure widens from one route to three.
That propagation is the difference between an architecture and a diagram.

## Running it

**Colab** — click the badge above. Everything is preinstalled.

**Locally** — Python 3.9+ with `numpy`, `pandas`, `matplotlib` (the first cell installs anything
missing):

```bash
jupyter notebook GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb
```

The lab is **fully offline and deterministic**. No API keys, no network calls, nothing that can
fail during a live session.

## Optional: running against a real model

The final section swaps the deterministic stub for a real OpenAI call. It is entirely optional and
safely gated — with no key present it prints a note and skips, and nothing earlier depends on it.

- **Colab** — 🔑 panel in the left sidebar → add a secret named `OPENAI_API_KEY` → enable notebook
  access.
- **Local** — `export OPENAI_API_KEY=...` before starting Jupyter.

Never paste a key into a cell; cell output is saved with the notebook.

The live cell re-runs the Stage 6 prompt-injection attack against a real model, with the poisoned
document still in the index. Both outcomes make the same point: if the model resists, good; if it
proposes the unauthorised action anyway, the provenance and policy checks refuse it — because the
proposal carries `origin="model:proposal"` rather than `origin="user:turn"`.

## Contents

| File | |
|---|---|
| `GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb` | the lab — 81 cells, 9 stages |
| `INSTRUCTOR_NOTES.md` | minute-by-minute run sheet, discussion prompts, expected questions |
| `deck/index.html` | 62-slide presenter deck sharing the notebook's spine |
| `docs/superpowers/specs/` | design spec for the redesign |

## A note on the numbers

Prices, throughput figures and the model-family capability table are **illustrative placeholders**,
gathered into a small number of constants so they can be replaced in one place with real quotes and
load-test results. Model names, context limits and pricing all drift — verify against current
provider documentation before any of this reaches a real design document.
