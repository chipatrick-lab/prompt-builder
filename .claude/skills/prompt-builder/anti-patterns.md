# Anti-Patterns Log

Known failure modes from past prompt-builder runs. The pre-mortem step (7.5) draws
from this list instead of reasoning from scratch. Add entries via Step 9 (auto-log)
whenever a prompt underperforms predictions.

---

## How the pre-mortem uses this file

1. Check trigger conditions against the current prompt
2. Rank matching entries by **priority score** (severity x base_rate)
3. Auto-apply patches for "always-patch" items (priority > 1.5)
4. Debate top 2 "active" items
5. Brainstorm ONE novel failure mode not in this log (avoids anchoring on known patterns)

---

## Entry format

```
### [Short name]
- **Failure:** What went wrong
- **Root cause:** Why the pre-mortem missed it
- **Patch:** What to add/change in prompts to prevent it
- **Applies when:** Trigger conditions (task type, domain, etc.)
- **Severity:** [1-3] (1=cosmetic, 2=material, 3=output-is-wrong)
- **Base rate:** [hits/opportunities] (how often this fires when triggered)
- **Priority:** severity x base_rate
- **Status:** always-patch | active | watch-list
- **First seen:** [date]
```

**Update rules:**
- After each run where trigger conditions were met: if it fired, increment hits; if
  not, increment opportunities only. Recalculate base_rate and priority.
- After 10+ observations: if base_rate < 0.1, demote to "watch-list" (skip in
  pre-mortem). If base_rate > 0.5 AND severity >= 2, promote to "always-patch"
  (apply automatically without debate).

---

## Entries

### Hallucinated specifics
- **Failure:** Model invents data, metrics, or examples it doesn't have access to
- **Root cause:** Prompt asks for "analysis" or "examples" without specifying source data or flagging when data is unavailable
- **Patch:** Add `[DATA GAP]` or `[PLACEHOLDER: ...]` instructions. Require the model to flag missing info rather than fabricate.
- **Applies when:** Any analysis, report, or document that references real-world data
- **Severity:** 3
- **Base rate:** ~0.7 (estimated)
- **Priority:** 3 x 0.7 = 2.1
- **Status:** always-patch
- **First seen:** starter

### Wrong altitude
- **Failure:** Model defaults to implementation details (sprint points, PRs, tickets) when the audience wants business outcomes (revenue, risk, velocity)
- **Root cause:** Prompt specifies "executive audience" in tone but not in content constraints
- **Patch:** Add explicit constraint: "Frame every point as a business outcome, not internal process metrics"
- **Applies when:** Any deliverable targeting VP+ audience
- **Severity:** 2
- **Base rate:** ~0.3 (estimated)
- **Priority:** 2 x 0.3 = 0.6
- **Status:** active
- **First seen:** starter

### Discussion-as-commitment
- **Failure:** Model treats discussed ideas as action items when extracting from transcripts/notes
- **Root cause:** No definition of what counts as a "commitment" vs. a "discussion point"
- **Patch:** Add definition: "An action item is a task explicitly committed to or assigned, not merely discussed or proposed"
- **Applies when:** Any extraction task (action items, decisions, takeaways from unstructured text)
- **Severity:** 3
- **Base rate:** ~0.5 (estimated)
- **Priority:** 3 x 0.5 = 1.5
- **Status:** active
- **First seen:** starter

---

## Calibration Ledger

Track every prompt-builder run outcome here. One line per run.

```
[date] | tier | score (x/12 = NN%) | outcome | topic
```

### Entries

(none yet — auto-populated by Step 9)

### Stats (recompute after every 5 entries)
- Total runs: 0
- Edit rate: -
- Mean bias: -
- Axis with most overestimation: -
