# Makers, Checkers & the 80/20 Loop

Full methodology for the expert-panel layer of the `prompt-builder` skill. The skill
convenes **makers** (a multidisciplinary panel that debates the framing) and **checkers**
(who ground and operationalize the result), looping until marginal benefit ≈ marginal
cost. This doc is loaded on demand to keep `SKILL.md` scannable.

Guiding principle: **never spend more forging the prompt than the prompt will save.**
The whole apparatus scales down to nothing for trivial asks and up for hard ones.

---

## 1. Complexity tiering (the "scale with complexity" lever)

Classify the request before assembling anyone. The tier sets panel size, round caps, and
whether to escalate to real sub-agents.

| Tier | Signals | Panel | Debate rounds (cap) | Checkers | Engine |
|------|---------|-------|---------------------|----------|--------|
| **1 — Simple** | single discipline, clear ask, low stakes, output format obvious | skip panel; at most one expert lens | 0 | light Operations check only | simulated |
| **2 — Moderate** | mild ambiguity, one useful cross-cutting angle, moderate stakes | primary + 1 degree-1 expert | 1 (cap 2) | Grounding + Operations | simulated |
| **3 — Complex** | genuinely multidisciplinary, high-stakes, contested, or spans ≥2 distant fields | primary + degree-1 + degree-2 | 2 (cap 3) | Grounding + Operations + Red-team | **real sub-agents** |

When unsure between two tiers, pick the lower one and let the loop's stop rule pull you
up only if quality is still climbing fast.

---

## 2. Assembling the makers (multidisciplinary panel)

1. **Primary expert** — name the core discipline the topic lives in, and the specific
   expert archetype (not just "economist" but "labor economist", if it sharpens the lens).
2. **Secondary experts by degree of separation:**
   - **Degree 1 — adjacent field.** Shares methods or vocabulary with the primary;
     covers blind spots the primary tends to have. *Economics → behavioral psychology,
     game theory, statistics.*
   - **Degree 2 — distant field, transferable lens.** Reframes the problem from an
     unexpected angle. *Economics → ecology (systems/equilibria), history (precedent),
     ethics (distributional fairness).*
3. **How many:** Tier 2 = primary + 1 (degree 1). Tier 3 = primary + 1 (degree 1) + 1
   (degree 2). Add a 4th only if a distinct, non-overlapping angle is clearly missing.
4. **Roster table** (always shown in the execution brief):

   | Expert | Discipline | Degree of separation | Why chosen | Distinct angle |
   |--------|-----------|----------------------|-----------|----------------|

Pick experts for **non-overlapping coverage**, not prestige. If two candidates would say
the same thing, keep one.

---

## 3. Debate protocol

1. **Opening framings** — each expert states how *they* would frame the prompt and what
   they'd insist it include or avoid.
2. **Cross-critique** — experts challenge each other's framings; the facilitator records
   genuine disagreements rather than smoothing them over.
3. **Convergence** — synthesize into a single **candidate prompt** using the
   `reference.md` skeleton. Where experts disagreed, either resolve explicitly or encode
   the tension as an instruction/constraint (e.g. "balance X against Y").

Keep each expert turn tight — a few sentences, not essays. The debate exists to improve
the prompt, and the prompt is the only deliverable.

---

## 4. Checkers — ground & operationalize

Checkers take the makers' candidate and make it real. They are a *separate* role from the
makers (different objective: rigor, not ideation).

- **Grounding checker** — Is every claim/assumption true and feasible? Strip speculation,
  vague superlatives, and instructions the model can't actually follow.
- **Operations checker** — Is it scoped, measurable, runnable? Pin **success criteria**,
  **output format**, length limits, and `{{VARIABLES}}`. Ensure another person could run
  it and judge the result. **Completeness guard:** before finalizing, verify the
  deliverable-hygiene pass removed only labels/filler — never a concrete checklist,
  worked example, or criteria definition. If coverage shrank, restore it; completeness
  beats brevity.
- **Red-team checker** (Tier 3) — How is this prompt misread, gamed, or derailed? What
  edge cases break it? Feed failures back to the makers.

---

## 5. The loop and the 80/20 stop rule

The loop alternates makers ↔ checkers. Stopping is an **economic** decision: continue
only while the marginal benefit of another round exceeds its marginal cost.

**Scoring.** After each round, score the candidate 0–100 across four axes (equal weight):
- **Clarity** — unambiguous to someone with no extra context.
- **Completeness** — all needed context, constraints, and criteria present.
- **Groundedness** — realistic, true, feasible; no hand-waving.
- **Operationality** — runnable and measurable; output format pinned.

**Stop when marginal benefit ≤ marginal cost.** Concretely, stop when **either**:
- the **delta** vs the previous round is **< ~5 points** (the 80/20 knee — you've captured
  ~80% of attainable quality and returns are flattening), **or**
- the tier's **round cap** is reached (hard cost bound).

This is the equilibrium rule: the first round usually buys a large jump; each subsequent
round buys less. When the next round's expected gain no longer pays for its cost (tokens,
latency, and — for Tier 3 — sub-agent calls), you're at equilibrium. Stop.

Record a **convergence log** (round, score, delta, note) so the stop decision is visible.

---

## 6. Hybrid execution engine

- **Default — simulated personas.** Claude role-plays the panel within one context.
  Cheap, fast, single context. Used for Tier 1–2.
- **Escalate — real sub-agents.** Spawn each expert/checker as a parallel `Agent`
  (general-purpose or Plan) so views are genuinely independent. Trigger when: **Tier 3**,
  the user **flags high-stakes**, or the topic **spans ≥2 distant disciplines**. Launch
  independent agents in parallel (one message, multiple calls). Announce the escalation in
  the execution brief, since it materially changes the cost.

---

## 7. Cost model (show BOTH forms)

Shown in the execution brief *before* the loop runs, and reconciled against actuals in the
convergence log.

**A. Calls / rounds (model-agnostic):**
```
total model calls = (experts × debate_rounds) + checker_passes [+ 1 synthesis]
```
For simulated personas these are *turns within one context*; for sub-agents they are
*separate agent runs* (much costlier) — label which.

**B. Token / $ estimate:**
1. Estimate tokens per turn (rough default: ~1–2k in, ~0.5–1k out per expert/checker turn;
   sub-agent turns run larger).
2. `total_tokens ≈ Σ turns × per-turn tokens`.
3. `cost ≈ input_tokens × input_price + output_tokens × output_price`.

Pull **current model IDs and per-token pricing from the `claude-api` skill** — do not
hardcode numbers that go stale. State the assumed model and pricing inline next to the
estimate, e.g. *"~28k tokens ≈ $0.32 (claude-sonnet-4-6, pricing per claude-api skill)."*
If pricing can't be retrieved, show the token estimate and say pricing is unconfirmed.

---

## 8. Worked sketch

**Topic:** "Design a carbon-tax policy prompt." → **Tier 3** (multidisciplinary, high-stakes).
- **Roster:** Environmental economist (primary) · Behavioral psychologist (deg 1 — how
  households actually respond) · Historian (deg 2 — precedent from past carbon/sin taxes)
  · Checkers: Grounding, Operations, Red-team.
- **Engine:** real sub-agents (Tier 3).
- **Rounds planned:** 2 debate + 3 checker passes ≈ 9 calls. Tokens ~30k ≈ $X.
- **Loop:** R1 score 72 → R2 87 (Δ15, keep) → R3 90 (Δ3 < 5 → **stop**). Captured the
  80/20 knee; a 4th round wasn't worth its cost.
