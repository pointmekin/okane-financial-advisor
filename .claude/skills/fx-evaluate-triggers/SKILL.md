---
name: fx-evaluate-triggers
description: Cross-reference new Knowledge Base inputs against open positions in agents/fx/portfolio.md. Checks stops, targets, and non-price invalidation conditions. Outputs a trigger alert table with severity ratings.
---

# FX Evaluate Triggers

## Purpose

FX does not monitor prices continuously. This skill checks whether new KB data (new macro reports, central bank news, economic calendar events) activates any trigger condition on open positions. It is the trip-wire, not the trader.

## Inputs

- New KB documents (identified by `fx-read-knowledge-base` in the current session)
- Open positions from `agents/fx/portfolio.md`
- Active plan files from `agents/fx/knowledge_base/plans/` where `status: open`

## Procedure

1. Read all active plan files to extract stop, target, and non-price invalidation conditions
2. For each position, scan new KB documents for evidence that any condition has been triggered
3. Compile results into the alert table below

## Output Format

```markdown
## Trigger Evaluation — <YYYY-MM-DD>

### Sources Scanned
- `<path1>` (<PAIR>, date)
- `<path2>` (general macro, date)

### Alert Table

| Pair | Direction | Trigger Type | Evidence Found | Severity | Recommended Action |
|------|-----------|--------------|----------------|----------|--------------------|
| EUR/USD | Long | Non-price invalidation: "ECB 50bps surprise" | ECB delivered 25bps only — not triggered | CLEAR | No action |
| GBP/USD | Short | Non-price invalidation: "UK CPI re-accelerates above 4%" | UK CPI: 3.2% — below threshold | CLEAR | No action |

### Severity Definitions

- **HIT-TARGET** — Price has reached the plan target. Close position.
- **HIT-STOP** — Price has reached the plan stop. Close position.
- **INVALIDATED** — Non-price invalidation condition clearly triggered. Close position regardless of current P/L.
- **WATCH** — Condition is partially met or trending toward breach. Flag for next FX_Weekly. No trade yet.
- **CLEAR** — No evidence of trigger in scanned documents.

### Summary
**Positions requiring immediate action:** X
**Watch flags:** X
**All-clear positions:** X
```

## Decision Rules After Evaluation

| Severity | What FX Does |
|----------|--------------|
| HIT-TARGET | Run `/fx-close <PAIR>` with reason HIT-TARGET. Update plan `status: closed-win`. |
| HIT-STOP | Run `/fx-close <PAIR>` with reason HIT-STOP. Update plan `status: closed-loss`. No Z.md entry — this is the system working. |
| INVALIDATED | Run `/fx-close <PAIR>` with reason INVALIDATED. Update plan `status: invalidated`. Invoke `fx-update-memory-ledger` only if the invalidation was missed due to a process error. |
| WATCH | Note in the plan's `## Execution Notes` section. Review at next FX_Weekly. No close. |
| CLEAR | No action. |

## Intellectual Honesty

If a scanned document does not contain data relevant to an invalidation condition, mark it CLEAR for this scan with a note:
> "[Invalidation condition] Not evaluable from this document — relevant event not discussed."

Never infer CLEAR from absence of negative data. Only mark INVALIDATED or HIT when positive evidence exists in a KB document.

Price-based triggers (stop/target) are checked against price data. If no current price data is in the KB, state this explicitly and do not mark them CLEAR or TRIGGERED. Recommend the user add current price context to the KB.
