---
name: fx-portfolio-allocator
description: Manage the FX $10,000 virtual portfolio. Use for any open, close, or stop-tighten action. Enforces all six invariants before writing to agents/fx/portfolio.md.
---

# FX Portfolio Allocator

## Portfolio Ledger Location

`agents/fx/portfolio.md` — the single source of truth for the FX agent's open positions.

## Ledger Schema

`agents/fx/portfolio.md` must always contain:

```markdown
---
total_capital: 10000
last_reviewed: <YYYY-MM-DD>
---

# FX Portfolio

> Managed by FX via the `fx-portfolio-allocator` skill.
> Rules: 0-5 positions, max $100 at risk per trade, max $500 total at-risk.

## Deviation Log

> Track any deviations from portfolio rules here. If portfolio meets all invariants, note: "No deviations."

## Open Positions

| Pair | Direction | Lots | Entry | Stop | Target | $ At Risk | Plan Link |
|------|-----------|------|-------|------|--------|-----------|-----------|

## Cash / Margin

| Account Balance ($) | Total At Risk ($) | Total At Risk (%) |
|---------------------|-------------------|-------------------|
| 10,000.00           | 0.00              | 0.0%              |

## Summary
- **Total open positions:** X
- **Total at risk:** $X.XX (X.X%)
- **Account balance:** $X,XXX.XX
```

## Invariant Checks (run BEFORE every write)

Run all six checks before any modification. If any fails, refuse the action and state which rule was violated.

| # | Rule | Block message |
|---|------|---------------|
| 1 | Positions after change: 0 ≤ n ≤ 5 | "BLOCKED: This action would result in N open positions. FX maximum is 5." |
| 2 | Per-trade risk ≤ $100 | "BLOCKED: This trade risks $X. Maximum per-trade risk is $100 (1% of $10,000)." |
| 3 | Total at-risk after change ≤ $500 | "BLOCKED: This action would raise total at-risk to $X. Maximum is $500 (5% of $10,000)." |
| 4 | A pending plan file exists at `agents/fx/knowledge_base/plans/<PAIR>-<date>.md` with `status: pending` | "BLOCKED: No pending plan found for <PAIR>. Run fx-generate-trade-plan first." |
| 5 | No correlated overlap with existing open positions | "BLOCKED: <PAIR> creates correlated overlap with open position <OTHER_PAIR>. These pairs move in the same direction under the same conditions." |
| 6 | No high-impact event within 24h on either currency in the pair | "BLOCKED: <EVENT> for <CURRENCY> is scheduled in <X>h. Wait for the event window to close before entering." |

## Correlation Reference

Pairs that should not be held simultaneously (they create duplicate directional exposure):

| If long... | Do NOT also hold... |
|------------|---------------------|
| EUR/USD | Short USD/CHF |
| GBP/USD | Short USD/CHF |
| EUR/USD | Long GBP/USD (same USD short — partial overlap, flag as WATCH not BLOCK) |
| AUD/USD | Long NZD/USD (commodity currency overlap — flag as WATCH not BLOCK) |

Full blocks apply to pairs that are near-perfect inverses. Partial overlaps are flagged as WATCH and left to judgment.

## Actions

### Open (buy/sell new position)
1. Run all six invariant checks
2. Calculate position size: `floor(risk_dollars / pip_value_per_lot / stop_distance_pips)`
3. Append row to Open Positions table
4. Update Total At Risk and Summary
5. Update plan `status` from `pending` to `open`
6. Update `last_reviewed` date

### Close (target hit / stop hit / invalidation / discretionary)
1. Remove row from Open Positions table
2. Update Total At Risk and Summary
3. Update plan `status` to `closed-win`, `closed-loss`, or `invalidated`
4. Update `last_reviewed` date

### Stop-tighten
1. Verify new stop is closer to entry than current stop (invariant: stops may only tighten)
2. Update stop price in the Open Positions row
3. Recalculate $ At Risk (may decrease as stop tightens)
4. Update Summary
5. Update `last_reviewed` date

## Price Handling

FX uses approximate current price for position sizing. When opening a trade, state the assumed entry price explicitly. Position size is calculated from the stop distance in pips and pip value, not from guessing price direction.

## Output

After every successful write, print the updated Open Positions table, Cash/Margin block, and confirm the action taken.
