You are running **FX_Setup_Scan** — evaluate setup quality for a specific pair.

Usage: `/fx-setup-scan <PAIR>`

Invoke the `fx` subagent with these instructions:

---

This is the FX_Setup_Scan trigger for **$ARGUMENTS**. Your job is to evaluate whether a tradable swing setup currently exists and grade it. This trigger does not open a trade — it only produces an assessment.

**Step-by-step:**

1. **Load current bias** — Read `agents/fx/WATCHLIST.md`. Check the current bias for $ARGUMENTS. If bias is `no-trade`, state: "FX_Setup_Scan cannot proceed — $ARGUMENTS is currently `no-trade` due to [reason from watchlist notes]. Wait for the event window to close."

2. **Read KB for this pair** — Invoke the `fx-read-knowledge-base` skill filtered to $ARGUMENTS across all KB folders: `agents/fx/knowledge_base/macro/`, `agents/fx/knowledge_base/pairs/$ARGUMENTS/`, and `agents/fx/knowledge_base/articles/`.

3. **Macro assessment** — Evaluate the macro context for $ARGUMENTS. Does the fundamental evidence (rate differentials, central bank stance, economic data) clearly support the directional bias in the watchlist? State: SUPPORTS / CONFLICTS / INSUFFICIENT.

4. **Technical assessment** — Based on any technical notes in `agents/fx/knowledge_base/pairs/$ARGUMENTS/`, evaluate the current chart structure. Does a swing setup exist at current levels? State: YES / WEAK / NO. Describe the setup if YES or WEAK.

5. **Risk/reward check** — If both macro and technical suggest a trade, estimate the setup's risk/reward:
   - Approximate entry zone
   - Logical stop placement (beyond S/R, not arbitrary)
   - Logical target (opposing S/R level, measured move, or trend extension)
   - Is the R:R ≥ 2.0? State the ratio.

6. **Grade the setup:**

```markdown
## Setup Scan — $ARGUMENTS — <YYYY-MM-DD>

### Grade: A | B | C | NO SETUP

**Grade Criteria:**
- A: Macro and technical fully aligned, clear entry/stop/target, R:R ≥ 2.5
- B: Macro and technical mostly aligned, setup clear but one element has uncertainty, R:R ≥ 2.0
- C: Weak confluence, one of macro/technical is neutral, R:R marginal (< 2.0)
- NO SETUP: Conflicting signals, no clear entry structure, or event window

### Macro Context
<Summary of macro evidence and stance>

### Technical Setup
<Description of setup: pattern, key levels, entry zone>

### Estimated Plan Parameters
- Entry zone: <prices>
- Stop: <price and rationale>
- Target: <price and rationale>
- R:R: <ratio>

### Recommendation
<Proceed to /fx-trade | Wait for [condition] | No trade — reason>
```

**Hard rules for this trigger:**
- Grades are based on KB content, not training-data market knowledge
- If insufficient KB research exists for $ARGUMENTS, state this explicitly and grade NO SETUP
- This trigger never writes to `agents/fx/portfolio.md` — no position is opened
