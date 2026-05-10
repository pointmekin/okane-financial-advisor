---
name: fx-watchlist-manager
description: Manage agents/fx/WATCHLIST.md — update pair directional bias, promote bias ratings, flag event windows, and track setup quality grades. Use when FX needs to update the monitoring status of forex pairs.
---

# FX Watchlist Manager

## Watchlist Location

`agents/fx/WATCHLIST.md`

Frontmatter:
```yaml
---
last_reviewed: <YYYY-MM-DD>
---
```

## Bias Lifecycle

```
neutral → long-bias or short-bias → (no-trade during event windows) → neutral
                                  → open (active position)
```

Bias changes require evidence from KB documents. Price movement alone never changes bias.

## Bias Definitions

| Bias | Meaning |
|------|---------|
| `long-bias` | Macro + technical evidence favor the long side. Watching for a specific technical trigger to enter. |
| `short-bias` | Macro + technical evidence favor the short side. Watching for a specific technical trigger to enter. |
| `neutral` | Insufficient KB evidence, mixed signals, or macro/technical in conflict. No directional lean. |
| `no-trade` | High-impact event within 24h affecting either currency in the pair, or signals are too conflicting to enter. Temporary. |

## Actions

### Update bias for a pair

Edit the `Bias` cell and `Notes` for the relevant pair row. All bias changes must include a one-line rationale in the Notes column citing the KB source.

Rules:
- Bias changes must be grounded in KB content (macro reports, technical notes), not price movement
- If the evidence changes direction, say so explicitly: "Bias flipped short: ECB surprised with dovish hold on [date]"
- `no-trade` is always temporary — specify when it expires (e.g., "no-trade until FOMC decision passes")

### Add a new pair to the watchlist

Append a row to the Watch List table:

```markdown
| PAIR | neutral | — | YYYY-MM-DD | No KB research yet. Add macro and technical notes to start analysis. |
```

The "Why Watching" note must describe a macro or structural hypothesis, not a price observation. Good: "Rate differential widening favors USD strength; AUD vulnerable to RBA dovish pivot." Bad: "AUD/USD is oversold."

### Set event-window no-trade

When a high-impact event is within 24 hours of either currency in the pair:
1. Set bias to `no-trade`
2. Note in the Notes column: the event name, date, and estimated clearance time
3. After the event window passes: reassess KB, restore appropriate bias

### Update setup grade

After `/fx-setup-scan` runs, update the `Setup Grade` column with the grade (A/B/C/—) and date.

### Mark active trade

When `/fx-trade` opens a position:
1. Update Notes: "Active trade open — see `agents/fx/knowledge_base/plans/<PAIR>-<date>.md`"
2. Bias remains as-is (the trade is the bias in action)

When `/fx-close` closes the position:
1. Remove the active trade note
2. Reassess bias based on current KB

## Passed Pairs

To move a pair to the Passed Pairs table:
1. Remove from the Watch List table
2. Add to Passed Pairs with a specific reason
3. Reason must be substantive: "USD/CHF: near-perfect inverse of EUR/USD — would always create correlated overlap. Will monitor indirectly via EUR/USD." Not: "Not interesting."

## Weekly Watchlist Review (called by FX_Weekly)

For each pair in the Watch List:
1. Check if new KB documents were added since `last_reviewed`
2. If yes: update bias and grade; promote to directional bias if evidence threshold is met
3. If no: leave unchanged — no nagging

Output:
```markdown
### FX Watchlist Review — <YYYY-MM-DD>
- **PAIR** (neutral → long-bias): rate differential data now supports USD strength; setup grade B.
- **PAIR** (no-trade → neutral): FOMC event window cleared; reassessing from neutral.
- **PAIR** (unchanged): no new KB docs this week.
```

Update `last_reviewed` in `agents/fx/WATCHLIST.md` frontmatter after every review.
