---
name: prompt-builder
description: Refine a rough request into a sharp, well-structured prompt and an execution plan before any work begins. Convenes a multidisciplinary panel of expert "makers" that debate the framing, plus "checkers" that ground and operationalize it, looping under an 80/20 stop rule. Use when the user wants to improve/tighten a prompt, plan an approach before submitting, draft a system prompt, or asks to "build", "generate", "refine", or "plan" a prompt. Based on Anthropic's prompt-generator methodology.
---

# Prompt Builder

Turn a rough idea into (1) a sharp prompt and (2) an execution plan. Between capturing
intent and finalizing, convene a **multidisciplinary expert panel** that debates the
framing (**makers**) and a separate role that grounds and operationalizes it
(**checkers**), iterating under an **80/20 stop rule** — so the request is well-formed
*before* work starts.

Based on Anthropic's prompt-generator / prompt-improver methodology. See `reference.md`
for the technique catalog, `frameworks.md` for the named-framework library, and
`makers-checkers.md` for the full panel methodology.

## When to use

- The user hands over a vague or one-line request and wants it sharpened first.
- The user wants to plan an approach and align before execution begins.
- The user is authoring a reusable prompt or system prompt for another LLM call.

## When NOT to use

- The request is already precise and the path is obvious — just do the work.
- The user explicitly says "just do it" / "no planning".

## Entry modes

Pick the mode before running the workflow:

- **BUILD** (default) — the user has a rough request; run the full workflow below.
- **RECOVER** — the user has a *good output* but lost/never had the prompt. Skip the
  panel; reverse-engineer the prompt from the sample (RPEF framework in `frameworks.md`).
- **CLARIFY-FIRST** — the user knows roughly what they want but can't specify it. Run an
  AI-led interview (Reverse-Role framework) to extract requirements *before* Step 2.

## Tier 0 — Fast path (30 seconds)

Before entering the full workflow, check: is this a straightforward request where a
single framework scaffold + intent capture is sufficient? If yes, run Tier 0:

1. Capture intent in one sentence
2. Pick one framework scaffold from `frameworks.md` based on intent
3. Fill the scaffold with reasonable defaults
4. Deliver the prompt. Done.

**Upgrade trigger:** If the output from the Tier 0 prompt disappoints (user says "Edit"
or asks for more), upgrade to Tier 1+ and run the full workflow on the same request.
Tier 0 is the "try it fast, upgrade if needed" path.

Use Tier 0 when: single-discipline, clear ask, obvious framework match, low stakes,
no ambiguity. If ANY of these fail, use Tier 1+.

---

## Full Workflow (Tier 1+)

Track these steps as a todo list and work through them in order. Do **not** start
executing the underlying task — this skill produces a prompt and a plan, then stops for
approval (Step 9).

### 1. Capture intent

Take the raw request from the skill args or the recent conversation. Restate it in one
sentence. If there's nothing to capture, ask the user for their draft request.

### 2. Diagnose

Silently analyze: **task type**, **intent category** (create / transform / reason /
critique / recover / clarify / agentic — see `frameworks.md`), **success criteria**,
**missing context**, **ambiguities & risky assumptions**, **out-of-scope risks**
(destructive actions, external publishing, large refactors). The intent category picks
the candidate framework scaffold used in Step 6.

### 3. Clarify (only what matters)

Ask **only the high-impact questions** via `AskUserQuestion`, with concrete options and
a recommended default. Skip anything you can answer from the codebase or sensible
defaults — state the default instead. If nothing is ambiguous, skip this step.

### 4. Assess complexity & assemble the panel

Classify the request into a **tier** (this is the "scale with complexity" lever — it
keeps simple asks cheap). See `makers-checkers.md` for the full rules.

| Tier | When | Panel | Debate rounds | Checkers | Engine |
|------|------|-------|---------------|----------|--------|
| **1 — Simple** | single-discipline, clear ask | skip panel (≤1 lens) | 0 | light | simulated |
| **2 — Moderate** | some ambiguity / one cross-cutting angle | primary + 1 degree-1 expert | 1 | 1 pass | simulated |
| **3 — Complex** | multidisciplinary / high-stakes | primary + degree-1 + degree-2 | 2–3 | full suite | **escalate to real sub-agents** |

**Assemble the makers** (Tier 2+):
- Identify the core discipline → name the **primary expert**.
- Add **secondary expert(s)** at an explicit **degree of separation**: *degree 1* =
  adjacent field that fills blind spots; *degree 2* = distant field with a transferable
  lens that reframes the problem. (E.g. economics topic → economist + behavioral
  psychologist (deg 1) + historian (deg 2).)
- **Domain shortcut:** for the recurring domains *finance, work, economics, investing,
  parenting*, start from the ready-made expert packs, framework picks, and groundedness
  flags in `domains.md` instead of assembling from scratch — then adjust.

**Panel bootstrap** (sensitivity is high; bad panel = 34-point quality drop):

Instead of assembling one panel and hoping, generate 3 candidate panels and select:

```
Panel A: "Domain-heavy" — primary + degree-1 both from core domain
Panel B: "Cross-pollinate" — primary from domain + degree-1 from adjacent field
Panel C: "Adversarial" — primary + degree-1 chosen to DISAGREE (different schools)
```

For each, state in one line: what unique contribution it makes + its blind spot.
Select using dominance: if one covers everything the others cover plus more, use it.
If no dominance, merge non-redundant contributions from the best two.

**For Tier 2:** generate mentally (3 bullet points), pick one. Adds ~30s.
**For Tier 3:** consider running top 2 panels in parallel (sub-agents) and merging.
**Domain pack shortcut:** if a domain pack exists in `domains.md`, use it as Panel A
and generate only one alternative (Panel B). Saves cost for recurring domains.

Show the bootstrap in the execution brief:

```
### Panel bootstrap
| Panel | Composition | Strength | Blind spot |
|-------|-------------|----------|------------|
| A     | ...         | ...      | ...        |
| B     | ...         | ...      | ...        |
| C     | ...         | ...      | ...        |
→ Selected: <A/B/C/merge> because <one sentence>
```

**Panel selection safeguards** (apply to the selected panel):

1. **Practitioner primary rule.** The primary must have done or directly managed the
   target task recently. Test: "Can this expert describe a typical Tuesday in the target
   role?" If no, pick someone closer to the work.

2. **Degree-1 relevance check.** The degree-1 must improve the *how* (structure, format,
   pedagogy, rigor), not dilute the *what* (domain substance).

3. **Diversity with bounds.** Creative/distant experts (degree-2) allowed only when the
   primary is well-grounded.

4. **Post-debate specificity audit.** After the makers round, count concrete nouns (tool
   names, durations, named processes). If density is low, inject: "What are the 5 most
   specific things missing that someone would need on day 1?"

**Hybrid escalation:** default to simulated personas. Escalate to **real parallel
sub-agents** (the `Agent` tool — general-purpose / Plan) when the request is Tier 3, the
user flags it high-stakes, or it spans ≥2 distant disciplines.

### 5. Show the execution brief — and confirm

Before running the loop, show the user:

```
## Execution brief
Tier: <1|2|3> — <one-line why>

### Expert roster
| Expert | Discipline | Degree of separation | Why chosen | Angle they add |
|--------|-----------|----------------------|-----------|----------------|
| ...    | ...       | primary / 1 / 2      | ...       | ...            |
| + Checkers: Grounding, Operations[, Red-team]                              |

### Rounds planned
- Debate rounds: <n>  · Checker passes: <n>  · Engine: simulated | sub-agents
- Stop rule: stop when a round's quality gain < ~5/100 (80/20 knee) or the cap is hit.

### Cost estimate (both forms)
- Calls/rounds: <experts> × <rounds> debate + <checker passes> = <total> model calls
- Tokens / $: ~<tokens> tokens ≈ $<amount>  (model: <id>, pricing per claude-api skill)
```

Pull current model IDs + pricing from the **`claude-api` skill** for the token/$ line —
do not hardcode stale numbers; state the model + pricing assumption inline. For Tier 1,
collapse this to a one-line note. Let the user adjust the roster/tier before proceeding.

### 5.5. Generate the naive prompt (baseline)

Before running the panel, generate a quick **naive prompt** (what you'd produce with zero
expert input, just basic clarification applied). Keep it short. This becomes the baseline
for measuring the panel's contribution. Show it in the delivery as the "without panel"
version.

### 5.6. Test run the naive prompt (empirical grounding)

Actually **execute** the naive prompt and show a 3-5 line sample of what it produces.
This grounds the entire subsequent debate in observable behavior rather than predictions.
The makers should react to "here is what the naive version actually produced" not "here
is what we think it would produce."

Show in delivery:
```
## Naive test run (sample output)
<3-5 lines of actual output from the naive prompt>
Key weakness observed: <one sentence>
```

This is the single highest-ROI addition: top engineers spend 70% of time reading
outputs, 30% on structure. Without this step, the panel debates in the abstract.

### 6. Makers debate → candidate prompt (with loop engineering)

Each expert frames the prompt from their lens → structured cross-critique → converge on
a **candidate prompt**. Surface real disagreements; don't average them away.

**Loop pattern selection.** Based on the intent category from Step 2, embed the
appropriate self-correction loop INTO the refined prompt itself (not just in the
workflow, but as an instruction the model follows when executing the prompt):

| Intent | Loop pattern to embed | Instruction to include |
|--------|----------------------|------------------------|
| CREATE | Multi-draft convergence | "Generate 3 approaches. State each tradeoff. Pick strongest, graft best elements from runners-up. Deliver synthesized version." |
| REASON / ANALYZE | Verify backward | "Answer, then verify by working backward from conclusion. If verification fails, correct and re-deliver." |
| TRANSFORM | Draft → compare | "Transform once. Then compress by 30%. Compare both. Deliver whichever preserves more signal." |
| CRITIQUE | Devil's advocate loop | "State your position. Then argue the strongest opposing case. Reconcile into a final balanced judgment." |
| AGENTIC | Escalation loop | "Try the simplest approach first. If it fails or is insufficient, escalate to a more complex method. Flag if still unresolved." |

Show in delivery which loop was embedded and why. If the prompt is Tier 1 or the task
is a simple lookup, skip the loop (over-iteration on trivial tasks wastes tokens).

**Encoding rule:** Loops inside prompts must be numbered steps with clear labels
(DRAFT/CRITIQUE/REVISE/DELIVER). Always instruct the model to suppress intermediate
work and show only the final output unless the user asks to see the process.

**Pick a framework scaffold first.** Based on the intent from Step 2, select a named,
research-backed framework from `frameworks.md` (e.g. CO-STAR for audience-driven content,
RISEN for multi-step procedures, Tree-of-Thought for compare-the-options reasoning, BAB
for rewrites) instead of reinventing structure each time. The makers fill the chosen
scaffold; fall back to the generic skeleton in `reference.md` (role / context /
instructions / constraints / thinking / output_format / examples) when no named framework
fits. Use `{{VARIABLES}}` for per-run parts. State which framework was chosen and why.

**Make the debate visible.** For each expert, state their contribution as ONE line:
what they added or changed, not their full reasoning. The user must see in 5 seconds
what the panel bought them.

### 7. Checkers ground & operationalize

Run the checkers over the candidate (see `makers-checkers.md`):
- **Grounding** — is it true/feasible? remove hand-waving.
- **Operations** — is it scoped, measurable, runnable? pin success criteria + output format.
- **Red-team** (Tier 3) — where does this prompt fail or get misread?

### 7.5. Pre-mortem the prompt (Tier 2+)

Assume this prompt was executed and produced a **bad result**. Ask:
1. What is the most likely reason it failed?
2. What's the second most likely reason?

Then patch the candidate prompt to defend against the top 1-2 failure modes.

Show in the delivery block:

```
## Pre-mortem

| Failure mode | Likelihood | Patch applied |
|--------------|-----------|---------------|
| ...          | ...       | ...           |
```

For **Tier 1**, skip this step UNLESS the output feeds automation, handoffs, or
downstream processes where errors compound (then run it even on simple asks).
For **Tier 3**, this overlaps with the Red-team checker; merge them into one pass
rather than running both separately.

When running the pre-mortem, consult `anti-patterns.md` (if it exists) for known
failure modes from past runs. Draw from the log rather than reasoning from scratch.

### 7.6. Stress-test + variance + chain (combined pass)

One quick pass covering three checks:

1. **Stress-test:** Would the prompt break on empty, ambiguous, or extremely long input?
   Fold the top edge case into the pre-mortem patch if it reveals a real failure mode.
2. **Variance check:** "If I ran this 5 times, would results converge?" If not, tighten
   constraints (format, length, criteria definitions) until repeated runs would align.
3. **Chain check** (only if prompt >200 words of instructions): "Better as 2-3 sequential
   prompts?" If yes, restructure as a chain. Otherwise, skip.

Show in delivery:

```
## Robustness checks
- Variance: <high/low> + what was tightened
- Chain: single / chained (N steps) + why
- Edge cases folded into pre-mortem: <yes/no>
```

Skip entirely for Tier 1 unless output feeds automation.

### 7.9. Exemplar injection

After all refinement passes, write or find ONE gold-standard output example (or a
partial example showing format/style/tone). Embed it in the prompt inside `<example>`
tags. Examples anchor model behavior more reliably than instructions for:
- Format adherence
- Style/tone matching
- Length calibration
- Edge-case handling

The example should be ~20-50% the length of the expected output. For Tier 1, a
3-line example suffices. For Tier 3, a full worked example is warranted.

Show in delivery which example was added and why it was chosen.

### 8. Score, stop, and deliver

**Scoring rubric (hybrid: binary questions → 0-100 display).**

Score each axis by answering 3 binary sub-questions. Count "yes" answers (0-3 per axis,
0-12 total). Map to 0-100 for display: `score_100 = (yes_count / 12) * 100`.

```
CLARITY (0-3):
  C1. Could someone with zero context execute this without asking a question? [y/n]
  C2. Are all pronouns/references resolved to specific nouns? [y/n]
  C3. Would two independent executors produce structurally identical results? [y/n]

COMPLETENESS (0-3):
  K1. Are all inputs the model needs provided or defaulted? [y/n]
  K2. Are all constraints and boundary conditions explicit? [y/n]
  K3. Would the output answer the user's actual question (not an easier one)? [y/n]

GROUNDEDNESS (0-3):
  G1. Does every factual claim have a source, or is uncertainty flagged? [y/n]
  G2. Are all instructions physically executable by the model? [y/n]
  G3. Are assumptions stated, not buried? [y/n]

OPERATIONALITY (0-3):
  O1. Are success criteria defined so pass/fail is unambiguous? [y/n]
  O2. Can the output be used immediately (no mandatory blanks)? [y/n]
  O3. Is there a worked example or format template? [y/n]
```

After scoring, run a quick **devil's advocate pass**: for each "yes," ask "could I
argue no?" If the counter-argument is compelling, downgrade. This simulates a second
rater and catches over-generous self-scoring.

**Stopping rule:** Stop when delta < 5 points (on the 0-100 scale) between rounds, or
the tier's round cap is reached. **Ceiling override (Tier 3 only):** if score is
already >83 (10/12), ask the user "Push for ceiling?" instead of auto-stopping. This
catches high-scoring complex prompts that could be excellent with one more round.

Then present:

```
⚡ Prompt Builder complete (Tier [1/2/3])

## Your refined prompt
<fenced prompt block — see deliverable hygiene below>

## Panel contribution (what the debate actually changed)

| Expert | Added / Changed | Without it, the prompt would... |
|--------|----------------|----------------------------------|
| ...    | ...            | ...                              |

## Pre-mortem

| Failure mode | Likelihood | Patch applied |
|--------------|-----------|---------------|
| ...          | ...       | ...           |

## Robustness checks
- Variance: <high/low> + what was tightened
- Chain: single / chained (N steps) + why
- Edge cases folded into pre-mortem: <yes/no>

## Loop embedded
- Pattern: <multi-draft convergence / verify backward / draft-compare / devil's advocate / escalation / none>
- Why: <one sentence>
- Cap: <N iterations max>

## Exemplar
<what example was embedded and why it was chosen>

## Naive vs. refined (side-by-side proof of value)

| Metric         | Naive (no panel) | Refined (with panel) |
|----------------|------------------|----------------------|
| Clarity        |                  |                      |
| Completeness   |                  |                      |
| Groundedness   |                  |                      |
| Operationality |                  |                      |
| **Overall**    | ~X/100           | ~Y/100               |

## What changed

| Change | Why | Expert angle |
|--------|-----|--------------|
| ...    | ... | ...          |

## Convergence log

| Round | Score | Delta | Note |
|-------|-------|-------|------|
- Stopped because: <delta < threshold | round cap>.  Actual cost vs estimate: <...>

## Quality comparison (original ask vs. refined)

| Metric         | Original ask | Refined |
|----------------|--------------|---------|
| Clarity        |          |         |
| Completeness   |          |         |
| Groundedness   |          |         |
| Operationality |          |         |
| **Overall**    | ~X/100   | ~Y/100  |

## Naive test run (sample output)
<3-5 lines of what the naive prompt actually produced>
Key weakness observed: <one sentence>

## Compression check
- Original prompt length: <N words>
- Can 30% be removed without losing score? <yes/no>
- If yes, compressed version: <show it>
- Recommendation: use <original/compressed>

## Plan
1. <step> … Risks: <...>  Verify by: <...>

## Key lesson
<one concrete prompting insight from this session — what the biggest improvement was,
why it worked, and how to apply it next time. Keep to 2-3 sentences max.>

## Language upgrade (teach me to speak like a prompt engineer)

Show what the user said vs. how a prompt engineer would phrase it:

| What you typed | Prompt engineer version | Why it's better |
|----------------|------------------------|-----------------|
| <user's raw words> | <precise phrasing> | <the principle> |

Then teach ONE prompt engineering term that applied:

**Term:** <word/phrase>
**Means:** <one-sentence definition>
**Example of using it naturally:** <show what they'd type next time>

The goal: after ~4 weeks, the user speaks like a prompt engineer naturally and
no longer needs the builder for routine prompts. Track their raw prompt quality
over sessions and tell them when they're ready for quiet mode.

Run this prompt? [Yes / Edit / Discard]
```

**Critical: Always show the full refined prompt BEFORE asking.** The point is for the
user to read it, understand the structure and choices, and learn what makes a good
prompt. Never skip the display step. Show prompt first, ask yes/no, then execute only
after approval.

Wait for the user's decision:
- **Yes** = execute the refined prompt and show the result
- **Edit** = user gives feedback, iterate
- **Discard** = answer the original request directly without the methodology

### 9. Auto-log outcome (mandatory, after user decision)

Immediately after the user says Yes/Edit/Discard, append one line to the
**calibration ledger** section of `anti-patterns.md`. This is automatic and
non-optional. It is how the system learns.

```
[date] | tier | score (x/12 = NN%) | outcome | topic
```

Outcome values:
- **run-no-edit** = user approved, prompt executed as-is (scoring was accurate)
- **run-with-edits** = user approved after requesting changes (scoring was generous)
- **discard** = user rejected the prompt entirely (scoring was wrong)

**Calibration correction (after 10+ entries):**
- Compute edit rate: `(run-with-edits + discard) / total`
- If edit rate > 50%, scoring is systematically too generous. Tighten the rubric by
  requiring the devil's advocate pass to downgrade at least 1 "yes" per round.
- Compute mean bias per axis if patterns emerge (e.g., always overestimates
  Operationality). Report in the execution brief.

**Retrospective (only when quality fell short):**
If outcome is "run-with-edits" or "discard," also log:

```
### Anti-pattern captured
- What went wrong: <one sentence>
- Why the pre-mortem missed it: <one sentence>
- Patch for next time: <one sentence>
- Severity: [1-3]  Base rate: 1/<opportunities so far>
```

This feeds the weighted anti-pattern index over time.

**Deliverable hygiene** (applies to the `## Refined prompt` block): the fenced block must
be **copy-paste ready with zero editing**. Strip scaffolding *labels and filler* —
framework headers ("CONTEXT:", "BEFORE:", "ROLE:") the model doesn't need, meta-commentary,
and padding. **Never strip substance:** keep every concrete, domain-specific element —
checklists, worked examples, severity/criteria definitions, named risk classes, edge
cases. Leaner means *no fluff, not less coverage* — a long prompt that is all signal is
correct, and completeness beats brevity when they conflict. Analysis lives *outside* the
block. The refined prompt is the one thing the user lifts out.

**Placeholders must not block use.** A `{{VARIABLE}}` is for *optional* personalization — it
must never be a hole the user is forced to fill before the prompt runs. Either give each
variable a sensible inline default (e.g. `{{TIME_HORIZON | default: 10 years}}`) or have
the prompt state a reasonable assumption and proceed. The refined prompt must produce a
useful result on a single paste with zero edits; personalizing it should only *improve*
the result, never be a precondition for getting one. (Measured failure mode: bare
unfilled placeholders crater copy-paste readiness and operationality.)

## Notes

- This is a *planning* skill — it produces a prompt and a plan; it does not silently
  start executing the underlying task.
- Honor the 80/20 spirit everywhere: never spend more forging the prompt than the prompt
  will save. Tier 1 should feel nearly as fast as the old linear flow.
- Deeper guidance: `reference.md` (techniques), `frameworks.md` (named frameworks +
  intent map), `domains.md` (expert packs for the user's recurring domains),
  `makers-checkers.md` (panel method), and `anti-patterns.md` (known failure modes
  from past runs — feeds the pre-mortem).
- To measure whether a change to this skill actually improves outcomes, use the
  **`skill-creator`** skill's eval harness (blind comparator + self-critiquing grader +
  trigger eval). Don't ship skill changes on vibes — score them.
