---
name: portfolio-allocator
description: Manage the $10,000 virtual portfolio. Use for any buy, sell, or trim action. Enforces the 3-10 position rule and 40% maximum cash limit before writing to portfolio.md.
---

# Portfolio Allocator

## Portfolio Ledger Location

`portfolio.md` — the single source of truth for Nick's current portfolio state.

## Ledger Schema

`portfolio.md` must always contain:

```markdown
---
total_capital: 10000
last_reviewed: <YYYY-MM-DD>
---

# Nick's Portfolio

## Holdings

| Ticker | Shares | Cost Basis ($/share) | Total Cost ($) | % Allocation | Thesis Link |
|--------|--------|----------------------|----------------|--------------|-------------|
| AAPL   | 10     | 175.00               | 1750.00        | 17.5%        | [[theses/AAPL]] |

## Cash

| Cash ($) | Cash (%) |
|----------|----------|
| 2500.00  | 25.0%    |

## Summary
- **Total positions:** X
- **Total invested:** $X,XXX.XX (XX.X%)
- **Cash:** $X,XXX.XX (XX.X%)
```

## Invariant Checks (run BEFORE every write)

Run all checks before modifying `portfolio.md`. If any check fails, refuse the action and explain why.

| # | Rule | Failure message |
|---|------|-----------------|
| 1 | Positions after change: 3 ≤ n ≤ 10 | "BLOCKED: This action would result in X positions. Nick must hold 3–10 positions at all times." |
| 2 | Cash after change: ≤ 40% of total capital | "BLOCKED: This action would raise cash to XX%. Maximum allowed is 40%." |
| 3 | Cash after change: > 0% (never 0 stocks) | "BLOCKED: Nick cannot be 100% invested with 0 cash buffer AND cannot go to 0 stocks." |
| 4 | Ticker has an active thesis file in `knowledge_base/theses/` | "BLOCKED: No thesis exists for <TICKER>. Run generate-thesis-and-kill-conditions first." |
| 5 | Ticker status in thesis file is `active` | "BLOCKED: Thesis for <TICKER> is marked '<status>'. Resolve before trading." |

## Actions

### Buy
1. Run invariant checks
2. Calculate shares = floor(allocation_dollars / approximate_price)
3. Append row to Holdings table
4. Subtract from Cash
5. Update Summary block
6. Update `last_reviewed` date

### Sell / Exit
1. Run invariant checks (position count after removal must be ≥ 3)
2. Remove row from Holdings table
3. Add proceeds to Cash
4. Update `status` in `knowledge_base/theses/<TICKER>.md` to `exited`
5. Update Summary block and `last_reviewed` date

### Trim
1. Run invariant checks
2. Reduce shares in Holdings row
3. Add partial proceeds to Cash
4. Update Summary block and `last_reviewed` date

## Price Handling

Nick_Init operates in **price-blind conviction mode**: allocate by percentage conviction first (e.g., "20% to AAPL"), then calculate shares using approximate current price. Nick never trades on price predictions — price is only used for share-count arithmetic.

For subsequent actions, use a reasonable current price estimate. If unsure, state the assumed price explicitly in the output.

## Output

After every successful write, print the updated portfolio summary table and confirm the action taken.
