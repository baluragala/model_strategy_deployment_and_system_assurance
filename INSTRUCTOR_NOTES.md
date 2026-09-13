# GenAI-C8-W1-S2 — Instructor run sheet
## Model Strategy, Deployment & System Assurance · 150 minutes

**Deck:** https://claude.ai/code/artifact/cd8233af-63df-4aa9-8862-c7df23711235
**Notebook:** `GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb`

Deck keys: `←` `→` navigate · `g` overview grid · `n` speaker notes · `f` fullscreen.
Slide numbers below are deck slides; stage numbers are notebook stages. They share one spine.

---

## Running it in Colab

The notebook carries an **Open in Colab** badge as its first cell. Before that badge works, push
the notebook to GitHub and set the path in one place — the `COLAB_TARGET` line at the top of the
deck/notebook generator, or directly in cell 0:

```
https://colab.research.google.com/github/OWNER/REPO/blob/main/GenAI_C8_W1S2_Model_Strategy_Deployment_System_Assurance.ipynb
```

Colab already has numpy, pandas, matplotlib and jinja2, so the install guard in Stage 0 is a no-op
there. Nothing else needs changing — the lab is offline and deterministic on Colab exactly as it is
locally.

## Optional: the live OpenAI cells

The last section swaps `stub_llm()` for a real OpenAI call. **It is entirely optional and safely
gated**: with no key present it prints a note and skips, and nothing earlier in the notebook
depends on it.

To enable in Colab: 🔑 panel in the left sidebar → add a secret named `OPENAI_API_KEY` → enable
notebook access. Locally: `export OPENAI_API_KEY=...` before starting Jupyter. Never paste a key
into a cell — cell output is saved with the notebook.

The live cell re-runs the **Stage 6 attack** against a real model with KB-009 still poisoned. Both
outcomes teach the same thing:

- the model ignores the injected directive → good, and the guardrails were never needed
- the model proposes `grant_access` anyway → provenance and policy refuse it, because the proposal
  carries `origin="model:proposal"` rather than `origin="user:turn"`

That is the point worth making out loud: **the architecture does not depend on which one happens.**

> Running the full golden set and red-team suite live costs money and takes minutes rather than
> seconds. Do that outside the session. The model constant is `OPENAI_MODEL` — verify it against
> current OpenAI docs, since model names drift.

---

## Before the session

1. Run the notebook top to bottom once. It takes ~40 seconds and needs no API keys or network.
   If `matplotlib` is missing it installs itself in the first cell.
2. Decide whether you are demoing the live OpenAI cells. If not, ignore them entirely.
3. Open the deck, press `n` once to confirm speaker notes appear, then press `n` again to hide.
4. Have both on screen: deck for narration, notebook for the three live moments (marked ▶ below).
5. Learners need the notebook open from the start — they run it alongside you.

---

## Run sheet

| Time | Block | Slides | Stages | What happens |
|---|---|---|---|---|
| 0:00–0:05 | Open | 1–5 | — | Recap, agenda, running case, the nine-stage map |
| 0:05–0:30 | 1 · Model class | 6–17 | 1, 2 | Workload spec → class decision. **2 discussions** |
| 0:30–0:55 | 2 · Families | 18–23 | 3 | Capability profiles, the funnel, four roles |
| 0:55–1:25 | 3 · Hosting | 24–34 | 4, 5 | Routing, capacity, latency, cost. **2 guided analyses** |
| 1:25–1:45 | 4 · Evaluation | 35–43 | 6, 7 | ▶ **The incident.** Then measuring it |
| 1:45–2:05 | 5 · Robustness | 44–50 | 8 | Red team, six controls, 62%→0%. **1 discussion** |
| 2:05–2:27 | 6 · Capstone | 51–59 | 9 + capstone | Gate, canary, ▶ **propagation**, ADR |
| 2:27–2:30 | Close | 60–62 | — | Rubric, three sentences, reading |

The five-minute open is drawn from block 1's thirty, and the close from block 6's twenty-five, so
the six agenda blocks still total 150 minutes exactly.

---

## The three live moments

These are the beats the session is built around. Do not rush them, and do not narrate them from
the slide alone — run the notebook cell.

### ▶ 1 · The incident (slides 37–39, notebook Stage 6)

Run the three demo cells in order. Beats one and two are deliberately reassuring: a clean answer,
then a correct tool call. Beat three is an ordinary employee asking an ordinary onboarding
question, and the system grants production access.

**Pause after it.** Then ask: *whose fault is this?* The answer is nobody's — no malicious user,
no jailbreak phrasing, no model failure, and the policy forbidding it (KB-005) was retrieved in
the same call. The defect is one line: `trusted=True` on retrieved content.

### ▶ 2 · The fix (slide 48, notebook Stage 8)

Re-run the same request against `V2`. The poisoned document is still in the index and still
retrieved. The directive is still in the context window. It simply has no authority.

Headline: *you did not make the model harder to fool; you made being fooled not matter.*

### ▶ 3 · The propagation (slides 55–57, notebook Capstone)

**Ask for predictions before you run it.** Set `sovereign_only=True` — one boolean — and re-run
`run_pipeline()`. Eight of fourteen tracked properties change: three of four model classes become
ineligible, the chosen class flips, the reasoning model is replaced, the private route dissolves,
the bill changes, and the latency failure widens from one route to all three.

Then show **challenge variant D** (slide 57), which reclassifies 65% of all traffic to Restricted *without*
the sovereignty rule. It sounds far bigger and moves almost nothing — because it alters a weight,
and weights can be absorbed. That contrast is the cleanest statement of the session's thesis.

---

## Discussions and guided analyses

| Slide | Min | Prompt | What you are listening for |
|---|---|---|---|
| 10 | 4 | Fill in the workload spec for your own system | The fields they *cannot* answer — usually `peak_rpm`, `data_mix`, and an agreed p95. That gap is the real finding. |
| 17 | 5 | Name the artefact that would settle the fragile criterion; what are the options for the 3% tail? | Push from opinions to artefacts: a benchmark on their golden set, a DPA clause, a quote. Collect tail options on a board before Stage 4 answers it. |
| 27 | 4 | Swap router checks 1 and 4, re-run | R8 goes to REASONING on a vendor endpoint — Restricted merger data leaves the enclave. Ask what the incident report says. |
| 31 | 6 | Three capacity/cost what-ifs | Q2 is the good one: raising utilisation to 0.9 removes the burst headroom that Stage 1's 5.8× burst ratio demands. |
| 50 | 5 | Which single control does the most work? Then: why ship only that one? | Provenance does most of it — but it fails open the moment someone adds a tool-result or memory block without thinking. Depth survives future changes. |

---

## Timing pressure

If you are running late, compress in this order:

1. **Block 2 (families)** — cut the family matrix (slide 20) to 30 seconds. The funnel and the
   four-roles slide carry the block; the matrix is reference material.
2. **Slide 29 (capacity arithmetic)** — state the conclusion (availability sized the fleet, not
   throughput) and move on.
3. **Slide 60 (rubric)** — hand out rather than read.

Never cut: the incident (39), the fix (48), the propagation (56), or *constraints are not
weights* (14). Those four are the session.

---

## Questions you should expect

**"Aren't these scores made up?"**
Yes, and say so plainly. The class profile and cost indices are illustrative. The *method* is the
deliverable: weights derived from the workload, hard constraints applied before scoring, and a
sensitivity analysis that tells you which number is worth real evidence. Invite them to change a
coefficient in the notebook and re-run — the method survives, which is the point.

**"Our model wouldn't fall for that injection."**
Probably true, and it does not change the architecture. A real model is better than the stub at
ignoring injected instructions; it is not *reliably* better, and reliability is what a security
control requires. The six controls cost almost nothing and do not depend on model behaviour.

**"Why is self-hosting so expensive here? That doesn't match what I've seen."**
Because the enclave is sized by the high-availability floor, not throughput — two replicas for
14.5 output tokens/sec. At 100× volume (challenge B) the economics invert and the class flips to
self-host on its own. The break-even is computed, not asserted.

**"The cheapest option is C. Why aren't we doing it?"**
Deliberate, and the best question in the session. C fails capability (Stage 3) and misses p95 on
*every* route (Stage 5). Naming what the more expensive option buys — and what it costs — is
exactly the deliverable.

---

## Notebook notes

- Fully offline and deterministic. No API keys, no network in the core path. Nothing can fail live.
- The final cell shows a real-provider adapter shape and is **not executed**.
- `PRICES`, `GEN_TOKENS_PER_SEC` and `ServingAssumptions` are gathered so learners can replace them
  with their own quotes and load-test results in one place.
- Stage 9 gates on `p95_latency_ms_worst_route` and **fails**. This is intentional: the latency
  trade-off raised in Stage 5 is never resolved, and the gate refuses to let it become an implicit
  decision. Expect someone to think it is a bug. It is the lesson.
