# Domain Packs

Ready-made starting rosters, framework picks, and groundedness flags for the user's
recurring domains. In Step 4 (panel assembly), start from the matching pack instead of
assembling experts from scratch — then adjust to the specific request. These are
*starting points*, not straitjackets; swap experts when the topic demands it.

Every pack carries **groundedness flags** the Grounding/Red-team checkers must enforce —
the domain-specific ways a prompt (and its eventual output) goes wrong.

---

## Finance (personal / household)

- **Primary:** Certified financial planner (cash flow, budgeting, debt, goals).
- **Degree 1:** Behavioral economist — spending habits, mental accounting, why budgets fail.
- **Degree 2:** Tax specialist — after-tax thinking, account types, deductions.
- **Frameworks:** BROKE (goals + measurable key results) or RISEN (a repeatable method).
- **Groundedness flags:** not personalized financial advice; surface assumptions (income
  stability, time horizon, risk tolerance); separate needs vs wants honestly; account for
  taxes, fees, and inflation; flag where a fiduciary/professional is warranted.

## Investing

- **Primary:** Portfolio strategist (asset allocation, diversification, rebalancing).
- **Degree 1:** Behavioral finance specialist — loss aversion, recency bias, market timing traps.
- **Degree 2:** Economic historian — market cycles, base rates, "this time is different" checks.
- **Frameworks:** Tree-of-Thought (compare allocation strategies) + Pre-Mortem (downside).
- **Groundedness flags:** **not investment advice**; demand explicit time horizon, risk
  tolerance, and liquidity needs; state fees/expense ratios and tax drag; use base rates,
  not forecasts; insist on diversification and downside/sequence-of-returns risk; flag
  survivorship bias and cherry-picked backtests.

## Economics (analysis / explanation)

- **Primary:** Macroeconomist (growth, inflation, rates, labor, policy).
- **Degree 1:** Financial-markets analyst — how theory meets prices and expectations.
- **Degree 2:** Economic historian / political economist — precedent and distributional effects.
- **Frameworks:** Step-Back (first principles before the answer) or CO-STAR for explainers.
- **Groundedness flags:** cite mechanisms and evidence, not vibes; give the strongest case
  on *both* sides; distinguish positive vs normative claims; state confidence and where
  economists genuinely disagree; avoid single-cause stories.

## Work (career / management / performance)

- **Primary:** Management/executive coach (goals, feedback, influence, decisions).
- **Degree 1:** Organizational psychologist — motivation, team dynamics, bias in reviews.
- **Degree 2:** HR / compensation specialist — process, fairness, leveling, negotiation.
- **Frameworks:** RACE or BROKE for deliverables; BAB for rewrites (reviews, emails).
- **Groundedness flags:** concrete evidence/examples over adjectives; tie claims to impact
  and metrics; account for audience and power dynamics; avoid generic corporate advice;
  keep it specific to the user's role and situation.

## Parenting

- **Primary:** Developmental psychologist (age-appropriate expectations, behavior).
- **Degree 1:** Pediatric / behavioral specialist — sleep, routines, regulation, health.
- **Degree 2:** Educator — learning, motivation, school context.
- **Frameworks:** CARE (context + rules + examples) or RISEN (a calm step-by-step approach).
- **Groundedness flags:** **evidence-based, not fads**; age-appropriate and developmentally
  realistic; no one-size-fits-all — ask about the specific child/context; prioritize safety;
  non-judgmental tone; flag when a pediatrician/professional should be involved.

---

## Cross-domain notes

- These domains overlap (investing ↔ finance ↔ economics; work ↔ economics). When a request
  spans two, that is a **Tier 3** signal — pull a primary from each and let them debate.
- The recurring degree-1 lens across the money domains is **behavioral** (why people don't
  act rationally) — include it whenever the request involves a human actually changing
  behavior, not just an abstract analysis.
- For anything touching money or a child's wellbeing, the **Red-team checker is mandatory**
  regardless of tier — these are the high-stakes cases where a confident-but-wrong prompt
  does real damage.
