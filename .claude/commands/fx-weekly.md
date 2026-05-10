You are running **FX_Weekly** — the weekly portfolio review.

Invoke the `fx` subagent with these instructions:

---

This is the FX_Weekly trigger. Your default posture is: **do nothing**. Review open positions, check if stops should be tightened, and reassess macro stance if needed.

**Step-by-step:**

1. **Check last review date** — Read `agents/fx/portfolio.md` and note `last_reviewed`. If this trigger has been run less than 5 days ago, state: "FX_Weekly was already run on <last_reviewed>. Run it once per week." Then stop.

2. **Scan new KB inputs** — Invoke the `fx-read-knowledge-base` skill, scoped to documents added since `last_reviewed`. Produce a structured summary of new inputs.

3. **Evaluate open positions** — Invoke the `fx-evaluate-triggers` skill against new KB docs and all plans in `agents/fx/portfolio.md`. For each open position:
   - Assess whether the macro thesis that justified the trade still holds
   - Check if the technical setup has evolved (is price near stop? near target?)
   - Evaluate the non-price invalidation condition

4. **Stop-tightening decisions** — If price has moved favorably and a higher stop level is defensible (trailing to break-even, or trailing to recent swing), invoke `fx-portfolio-allocator` to update the stop. **Stops may only be tightened, never widened.**

5. **Thesis failures** — If new KB data invalidates the macro reasoning behind an open position (non-price invalidation triggered), close the position via `fx-portfolio-allocator`. Update the plan `status` to `invalidated`. If this reveals a process error, invoke `fx-update-memory-ledger`.

6. **Consecutive loss review** — If three or more consecutive losses have occurred since the last weekly review, do a full macro re-assessment before allowing new trades. State: "Three-loss streak detected. Reviewing macro stance before clearing new entries." If macro thesis still holds, state it is cleared. If uncertain, recommend waiting.

7. **Update watchlist** — Invoke `fx-watchlist-manager` to update biases for any pair with new KB inputs. Update `last_reviewed` in `agents/fx/WATCHLIST.md`.

8. **Update dates** — Update `last_reviewed` in `agents/fx/portfolio.md`.

9. **Output** — Weekly summary:
   - Open positions reviewed (status for each)
   - Stop tightenings executed (if any)
   - Positions closed (if any), with reason
   - Bias changes (if any)
   - Consecutive loss status

**Hard rules for this trigger:**
- Stops can only move in the direction of the trade — never widen
- "Position is down" alone is not a reason to close — only invalidation or stop-hit
- Bias changes must be based on new KB content, not this week's price action
