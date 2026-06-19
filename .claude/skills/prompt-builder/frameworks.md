# Named Framework Library

Curated, research-backed prompt frameworks for the `prompt-builder` skill, organized by
**intent**. In Step 2 (Diagnose) classify the request's intent; in Step 6 the makers pick
a framework from the matching row as the scaffold, then fill it.

This is a *curated* subset (~14 of the highest-leverage frameworks). The full 27-framework
library — with per-framework reference docs and copy-paste templates — ships in the
installed **`prompt-architect`** skill (`.claude/skills/prompt-architect/`), adapted from
[ckelsoe/prompt-architect](https://github.com/ckelsoe/prompt-architect) (MIT). When a
listed framework needs detail, read `prompt-architect/references/frameworks/<name>.md`.

## Intent → framework cheat sheet

| Intent | Use when… | Framework | One-liner |
|--------|-----------|-----------|-----------|
| **CREATE** | ultra-minimal one-off | **APE** | Action · Purpose · Expectation |
| **CREATE** | simple, expertise-driven | **RTF** | Role · Task · Format |
| **CREATE** | audience/tone/style critical | **CO-STAR** | Context · Objective · Style · Tone · Audience · Response |
| **CREATE** | multi-step procedure/method | **RISEN** | Role · Instructions · Steps · End-goal · Narrowing |
| **CREATE** | business deliverable with KPIs | **BROKE** | Background · Role · Objective · Key-results · Evolve |
| **CREATE** | strict do/don't compliance | **TIDD-EC** | Task · Instructions · Do · Don't · Examples · Context |
| **TRANSFORM** | rewrite / refactor / convert | **BAB** | Before · After · Bridge |
| **TRANSFORM** | compress / densify | **Chain of Density** | Iteratively pack in detail at fixed length |
| **REASON** | linear step-by-step | **Chain of Thought** | "Think step by step," then answer |
| **REASON** | compare distinct options | **Tree of Thought** | Branch, evaluate, prune paths |
| **REASON** | needs first principles first | **Step-Back** | Abstract to the governing principle, then solve |
| **CRITIQUE** | iterative quality lift | **Self-Refine** | Generate → feedback → revise loop |
| **CRITIQUE** | find failure modes early | **Pre-Mortem** | Assume it failed; work backward to causes |
| **CRITIQUE** | strongest opposing case | **Devil's Advocate** | Argue hard against the position |
| **RECOVER** | reverse-engineer a prompt | **RPEF** | Infer the prompt from a good output sample |
| **CLARIFY** | user can't specify yet | **Reverse-Role** | The AI interviews the user first |
| **AGENTIC** | tool-use, each result feeds next | **ReAct** | Reason → Act → Observe cycles |

## How this layers with the panel

- The **makers** don't just argue framing — they argue *which framework* fits and how to
  fill it. A framework is a proven skeleton; the panel supplies domain-specific content.
- Several **checker** roles map directly to critique frameworks: the Red-team checker is
  **Pre-Mortem** + **Devil's Advocate**; the loop's iterate step is **Self-Refine**.
- Frameworks compose. CO-STAR (for audience/tone) + RISEN's Steps (for method) is a common
  hybrid; note the combination explicitly when used.

## Picking well (don't over-frame)

A framework earns its place only when there's a gap between what the user *asked for* and
what they *need*. For Tier 1 / trivial asks, the lightest scaffold (APE or RTF) or none at
all is correct — adding CO-STAR to "translate this sentence" is pure overhead. This mirrors
the skill's 80/20 discipline: structure has a cost; spend it only where it pays.
