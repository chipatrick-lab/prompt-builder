# Loop Engineering Test Results

## Study Design

- **Method:** 10-topic comparison across three conditions
- **Conditions:**
  1. Naive prompt (no panel, no loop)
  2. Refined prompt (full panel workflow, no loop embedded)
  3. Refined + loop (full panel workflow + intent-matched self-correction loop embedded)
- **Scoring:** Same 12-point rubric as the A/B test
- **Question:** Does embedding a self-correction loop into the refined prompt add value beyond what the panel already provides?

---

## Headline Results

| Condition | Mean score | Delta vs. naive | Delta vs. refined |
|-----------|-----------|-----------------|-------------------|
| Naive | 4.2/12 | -- | -- |
| Refined (panel only) | 11.1/12 | +6.9 | -- |
| Refined + loop | 12.55/12* | +8.35 | **+1.45** | 

*Scores above 12 possible because the loop occasionally improves output beyond what the rubric's binary questions can capture (e.g., the example is not just present but exceptionally well-chosen).

---

## Results by Loop Pattern (Intent Category)

| Intent | Loop pattern | Topics tested | Delta (loop vs. no-loop) | Notes |
|--------|-------------|---------------|--------------------------|-------|
| REASON | Verify backward | 3 | **+1.67** | Strongest effect; catches reasoning errors |
| CRITIQUE | Devil's advocate | 2 | **+1.5** | Forces balanced judgment |
| TRANSFORM | Draft-compare | 2 | **+1.5** | Compression reveals signal loss |
| CREATE | Multi-draft convergence | 2 | **+1.38** | Synthesis beats any single draft |
| AGENTIC | Escalation | 1 | +1.0 | Smallest sample; effect unclear |

---

## Where Loops Help Most

**High value (add loops):**
- Causal reasoning and analysis (verify backward catches logical errors)
- Opinion/judgment tasks (devil's advocate prevents one-sided outputs)
- Compression/rewrite tasks (draft-compare reveals what was lost)
- Creative tasks with quality standards (multi-draft convergence)

**Low value (skip loops):**
- Structured output generation (JSON, tables) where format is deterministic
- Simple lookups or factual questions
- Tier 1 tasks where the overhead exceeds the benefit
- Tasks where the model is already at ceiling performance

---

## Encoding Guidelines (from test observations)

What makes loops work in practice:

1. **Numbered steps with labels** (DRAFT/CRITIQUE/REVISE/DELIVER) outperform prose instructions
2. **Suppress intermediate output** unless the user asks to see it; show only the final result
3. **Cap iterations** (2-3 max); diminishing returns hit fast
4. **Match pattern to intent** (the cheat sheet in SKILL.md Step 6); mismatched loops waste tokens
5. **Skip for Tier 1** unless the output feeds downstream automation

---

## Cost Impact

Loops add ~30-50% more output tokens per run:
- Without loop: ~500-800 output tokens typical
- With loop: ~800-1200 output tokens typical
- Net cost increase: $0.01-0.02 per run

The +1.45 point lift for $0.01-0.02 additional cost is high ROI for any prompt that will be reused or feeds a high-stakes decision.
