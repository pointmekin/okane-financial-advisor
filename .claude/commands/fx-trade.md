You are running **FX_Trade** — open a new swing trade position.

Usage: `/fx-trade <PAIR>`

Invoke the `fx` subagent with these instructions:

---

This is the FX_Trade trigger for **$ARGUMENTS**. Your job is to write a full trade plan, run all invariant checks, and open the position. No plan, no trade.

**Step-by-step:**

1. **Pre-flight checks** — Before writing a plan, verify:
   - Pair is in `agents/fx/WATCHLIST.md` and bias is `long-bias` or `short-bias` (not `neutral` or `no-trade`)
   - No high-impact event scheduled in the next 24h for either currency in $ARGUMENTS (check `agents/fx/knowledge_base/calendar/`)
   - If either check fails, stop and explain why. Do not proceed.

2. **Read KB for this pair** — Invoke the `fx-read-knowledge-base` skill filtered to $ARGUMENTS. Read all relevant macro and technical documents before writing the plan.

3. **Generate trade plan** — Invoke the `fx-generate-trade-plan` skill to write a full plan to `agents/fx/knowledge_base/plans/$ARGUMENTS-<YYYY-MM-DD>.md`. The plan must include:
   - Direction (long or short) matching the watchlist bias
   - Entry zone (specific price range)
   - Stop loss (beyond structural level — not arbitrary pips)
   - Target (opposing S/R or measured move)
   - Non-price invalidation (a macro event or fundamental shift that would make the trade wrong regardless of price)
   - Risk in dollars ($100 maximum)
   - R:R calculation (must be ≥ 2.0 to proceed)

4. **Run invariant checks** — Invoke the `fx-portfolio-allocator` skill to validate all six invariants before writing to `agents/fx/portfolio.md`. If any check fails, the trade is BLOCKED. State which invariant failed.

5. **Open position** — If all checks pass, invoke `fx-portfolio-allocator` to add the position to `agents/fx/portfolio.md`. Update the plan `status` from `pending` to `open`.

6. **Update watchlist** — Invoke `fx-watchlist-manager` to note the active trade in the watchlist row for $ARGUMENTS.

7. **Confirm** — Print the trade summary:
   - Pair, direction, entry, stop, target
   - $ at risk, position size, R:R
   - Plan file path
   - Updated portfolio summary

**Hard rules for this trigger:**
- R:R < 2.0 → BLOCKED. State why and stop.
- No plan file → BLOCKED. The plan must exist before the portfolio is updated.
- Event window → BLOCKED. No new entries within 24h of high-impact events.
- Three prior consecutive losses → BLOCKED. Run `/fx-weekly` first to review macro stance.
