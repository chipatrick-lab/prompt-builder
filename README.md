# Prompt Builder

A Claude skill that turns vague requests into sharp, high-scoring prompts by convening a simulated expert panel, running structured debate, and iterating under an 80/20 stop rule.

In a 50-topic A/B test, prompts refined by this skill scored **+6.9 points higher** (on a 12-point rubric) than naive prompts, with a Cohen's d of 3.91. That places outputs in the top 2-3% of prompt engineering quality.

---

## Before / After

**User's raw request:**
> "Help me write a budget"

**Naive prompt (score: 2/12):**
> "Create a monthly budget for me."

**Prompt Builder output (score: 11/12):**
> You are a certified financial planner helping a dual-income household build their first monthly budget.
>
> Context: The household earns $X/month after tax, has $Y in fixed expenses, and wants to save for a home down payment within 3 years. They have no existing budget system.
>
> Instructions:
> 1. Categorize all expenses into Needs / Wants / Savings using the 50/30/20 framework as a starting baseline, then adjust based on the stated goal priority.
> 2. For each category, provide a specific dollar target (not a percentage range).
> 3. Identify the top 3 spending leaks most likely to derail this budget based on behavioral economics research (e.g., subscription creep, lifestyle inflation, mental accounting errors).
> 4. Include a "first week" implementation checklist with concrete actions (which accounts to open, which automations to set, which apps to use).
>
> Constraints: Do not provide personalized financial advice. State all assumptions explicitly. Flag where a fiduciary professional would add value. Account for taxes on savings interest.
>
> Output: A markdown table of the budget + the 3 spending leaks + the implementation checklist. Total length: 400-600 words.
>
> Self-check: After drafting, verify each budget line sums correctly and that the savings rate achieves the stated goal within the time horizon. If not, adjust and re-deliver.

The naive version is vague, has no constraints, no format, no success criteria, and will produce generic output. The refined version is copy-paste ready and will produce actionable, specific results on the first run.

---

## Key Features

- **Panel bootstrap with sensitivity mitigation.** Generates 3 candidate expert panels (domain-heavy, cross-pollinate, adversarial) and selects by dominance. This mitigates a measured 34-point quality swing from bad panel selection down to ~3 points of variance.

- **Complexity tiering.** Scales from Tier 0 (30-second fast path) through Tier 3 (real parallel sub-agents). Never spends more forging the prompt than the prompt will save.

- **Loop engineering.** Embeds intent-matched self-correction loops directly into the refined prompt (verify backward for reasoning, devil's advocate for critique, multi-draft convergence for creation). Adds +1.45 points on top of the panel's contribution.

- **12-point binary scoring rubric.** Measures Clarity, Completeness, Groundedness, and Operationality with 3 yes/no questions per axis. Includes a devil's advocate pass to prevent over-generous self-scoring.

- **Pre-mortem + anti-pattern log.** Assumes the prompt failed and patches against the top 2 failure modes. Draws from a growing log of observed failures rather than reasoning from scratch.

- **Naive test run (empirical grounding).** Actually executes the naive prompt before the panel debates, so experts react to observed weaknesses rather than predictions.

- **Language upgrade system.** After each run, shows the user what they typed vs. how a prompt engineer would phrase it, plus teaches one new term. Goal: user independence within ~4 weeks.

- **Calibration ledger.** Tracks every run outcome (approved as-is, edited, discarded) and computes edit rate to detect systematic scoring bias.

---

## Test Results

### 50-Topic A/B Test

| Metric | Value |
|--------|-------|
| Mean improvement | +6.9 points (12-point scale) |
| 95% CI | [6.4, 7.4] |
| Cohen's d | 3.91 (very large) |

### By Complexity Tier

| Tier | Delta |
|------|-------|
| Tier 1 (Simple) | +4.9 |
| Tier 2 (Moderate) | +7.3 |
| Tier 3 (Complex) | +8.6 |

### Loop Engineering (10-topic follow-up)

| Intent pattern | Additional lift |
|----------------|----------------|
| REASON (verify backward) | +1.67 |
| CRITIQUE (devil's advocate) | +1.5 |
| TRANSFORM (draft-compare) | +1.5 |
| CREATE (multi-draft convergence) | +1.38 |

### Panel Sensitivity

| Scenario | Impact |
|----------|--------|
| Worst-case panel (no bootstrap) | -34 points |
| With 3-panel bootstrap | Reduced to ~3 points variance |

### Cost

$0.04-0.08 per run (Claude Sonnet pricing, simulated personas).

Full test methodology and data in [`tests/`](./tests/).

---

## How It Works

```
1. Capture intent        → Restate the request in one sentence
2. Diagnose              → Classify intent (create/transform/reason/critique/recover/clarify/agentic)
3. Clarify               → Ask only high-impact questions (skip if defaults work)
4. Panel bootstrap       → Generate 3 candidate panels, select by dominance
5. Execution brief       → Show tier, roster, rounds, cost estimate. STOP for confirmation.
5.5 Naive prompt         → Generate baseline (no expert input)
5.6 Naive test run       → Execute naive prompt, show actual output weaknesses
6. Makers debate         → Experts frame prompt from their lenses, cross-critique, converge
7. Checkers              → Ground (strip speculation) + Operationalize (pin format/criteria)
7.5 Pre-mortem           → Assume failure, patch top 2 modes
7.6 Robustness           → Variance check + chain check + stress test
7.9 Exemplar injection   → Embed one gold-standard example
8. Score + stop          → 12-point rubric, stop at 80/20 knee
9. Deliver               → Show refined prompt + naive comparison + key lesson
```

The user approves, edits, or discards. Only then does execution begin.

---

## Installation

### Claude Code (CLI)

Copy the skill directory into your Claude Code skills folder:

```bash
# From the repo root
cp -r .claude/skills/prompt-builder ~/.claude/skills/prompt-builder
```

The skill will be available as `/prompt-builder` in Claude Code sessions.

### claude.ai (Projects)

1. Create a new Project (or open an existing one)
2. Upload these files as **Project Knowledge**:
   - `.claude/skills/prompt-builder/domains.md`
   - `.claude/skills/prompt-builder/frameworks.md`
   - `.claude/skills/prompt-builder/makers-checkers.md`
   - `.claude/skills/prompt-builder/reference.md`
   - `.claude/skills/prompt-builder/anti-patterns.md`
3. Paste the contents of [`claude-ai/PROJECT_INSTRUCTIONS.md`](./claude-ai/PROJECT_INSTRUCTIONS.md) into the Project's **Custom Instructions** field

This gives you the full workflow with the gate, visible steps, and scoring rubric in every conversation within that project.

---

## Framework Library

The skill includes 14 curated, research-backed frameworks organized by intent:

| Intent | Frameworks |
|--------|-----------|
| CREATE | APE, RTF, CO-STAR, RISEN, BROKE, TIDD-EC |
| TRANSFORM | BAB, Chain of Density |
| REASON | Chain of Thought, Tree of Thought, Step-Back |
| CRITIQUE | Self-Refine, Pre-Mortem, Devil's Advocate |
| RECOVER | RPEF |
| CLARIFY | Reverse-Role |
| AGENTIC | ReAct |

The panel selects the framework; you don't need to memorize them.

---

## Domain Packs

Pre-built expert rosters for recurring topics. Each includes a primary expert, degree-1 and degree-2 experts, recommended frameworks, and domain-specific groundedness flags:

- **Finance** (personal/household)
- **Investing**
- **Economics** (analysis/explanation)
- **Work** (career/management/performance)
- **Parenting**

Add your own domain packs by editing `domains.md`.

---

## Customization

**Add domain packs:** Edit `domains.md` with your recurring topics. Each pack needs a primary expert, degree-1/2 experts, framework picks, and groundedness flags.

**Tune the anti-patterns:** The `anti-patterns.md` file grows automatically as you use the skill. After 10+ runs, it becomes a personalized failure-mode database that makes the pre-mortem increasingly accurate.

**Adjust the scoring rubric:** The 12 binary questions in SKILL.md can be modified if your use case has different quality dimensions.

---

## File Structure

```
.claude/skills/prompt-builder/
  SKILL.md              # Core skill instructions (the main file)
  domains.md            # Pre-built expert rosters by domain
  frameworks.md         # 14 named frameworks organized by intent
  makers-checkers.md    # Full panel methodology
  reference.md          # Prompt engineering technique catalog
  anti-patterns.md      # Failure mode log (grows with use)

claude-ai/
  PROJECT_INSTRUCTIONS.md   # Paste into claude.ai project instructions

tests/
  ab-test-results.md        # 50-topic A/B test data
  loop-engineering-results.md  # Loop pattern effectiveness data
```

---

## License

MIT. See [LICENSE](./LICENSE).
