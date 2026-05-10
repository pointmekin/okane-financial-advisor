You are running **FX_Close** — close an open position.

Usage: `/fx-close <PAIR>`

Invoke the `fx` subagent with these instructions:

---

This is the FX_Close trigger for **$ARGUMENTS**. Your job is to close the position, update all state files, and append a closure note to the trade plan.

**Step-by-step:**

1. **Verify position is open** — Read `agents/fx/portfolio.md`. If $ARGUMENTS is not in the open positions table, state: "No open position found for $ARGUMENTS." Stop.

2. **Determine closure reason** — Ask the user (or infer from context) why the position is being closed:
   - HIT-TARGET — price reached the plan target
   - HIT-STOP — price hit the stop loss
   - INVALIDATED — non-price invalidation condition was triggered
   - DISCRETIONARY — manually closed for a reason not covered above

3. **Close the position** — Invoke `fx-portfolio-allocator` to remove the position from `agents/fx/portfolio.md` and update the portfolio summary.

4. **Update the plan file** — Read the trade plan at `agents/fx/knowledge_base/plans/$ARGUMENTS-<plan_date>.md`. Update the `status` frontmatter field:
   - HIT-TARGET → `closed-win`
   - HIT-STOP → `closed-loss`
   - INVALIDATED → `invalidated`
   - DISCRETIONARY → `closed-loss` or `closed-win` as appropriate

   Append to the `## Execution Notes` section:
   - Date of closure
   - Closure reason
   - Exit price and result (profit/loss in dollars)
   - Whether the plan was executed as written

5. **Mistake ledger check** — Invoke `fx-update-memory-ledger` ONLY if the closure revealed a process violation:
   - Position was opened against a rule (correlated overlap, event window, no plan)
   - Stop was widened at any point during the trade
   - Non-price invalidation was missed and only caught post-hoc
   - Three consecutive losses without a weekly review
   - Do NOT write a Z.md entry for a clean stop-out — that is the system working.

6. **Update watchlist** — Invoke `fx-watchlist-manager` to remove the "active trade" note from $ARGUMENTS's watchlist row.

7. **Confirm** — Print the closure summary:
   - Pair, direction, entry, exit, P/L in dollars
   - Closure reason
   - Plan file updated
   - Whether a Z.md entry was written (and why, or why not)

**Hard rules for this trigger:**
- The plan file must be updated before this trigger is considered complete
- A clean stop-out never triggers a Z.md entry — do not conflate discipline with mistakes
