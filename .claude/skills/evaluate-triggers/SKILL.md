---
name: evaluate-triggers
description: Cross-reference new Knowledge Base inputs against the active Kill Conditions for all held stocks. Use during Nick_Weekly and Nick_Quarterly to detect if any position requires immediate review. Outputs an alert table.
---

# Evaluate Triggers

## Purpose

Nick does not monitor daily prices. He monitors business conditions. This skill is the mechanism by which incoming KB data (new filings, transcripts, articles) is checked against each held stock's Kill Conditions. It is the trip-wire, not the analyst.

## Inputs

- New KB documents (identified by `read-knowledge-base` in the current session)
- All active thesis files: `knowledge_base/theses/*.md` where `status: active`
- Current `portfolio.md` holdings table

## Procedure

1. Read all active thesis files to extract Kill Conditions
2. For each Kill Condition, scan the new KB documents for evidence of breach
3. Compile results into the alert table below

## Output Format

```markdown
## Trigger Evaluation — <YYYY-MM-DD>

### Sources Scanned
- `<path1>` (TICKER, date)
- `<path2>` (TICKER, date)

### Alert Table

| Ticker | Kill Condition | Evidence Found | Severity | Recommended Action |
|--------|---------------|----------------|----------|--------------------|
| DUOL   | [KC-1] DAU growth < 10% for 2 consecutive quarters + paid penetration stalls | Q1 DAU growth: 21% YoY, paid penetration 9.1% (rising) | CLEAR | No action |
| GOOG   | [KC-2] Cloud revenue growth decelerates below 15% for 2 consecutive quarters | Q1 Cloud growth: 28% (single quarter, above threshold) | CLEAR | No action |

### Severity Definitions
- **BREACH** — Kill Condition clearly and definitively met. Exit required after Nick's own confirmation read.
- **WATCH** — Kill Condition partially met or trending toward breach. Flag for Quarterly review.
- **CLEAR** — No evidence of breach in scanned documents.

### Summary
**Breached KCs requiring action:** X
**Watch flags:** X
**All-clear positions:** X
```

## Decision Rules After Evaluation

| Severity | What Nick Does |
|----------|----------------|
| BREACH | Read the full source document. If breach confirmed after full read: update thesis `status` to `broken`, exit via `portfolio-allocator`, append to `Z.md` via `update-memory-ledger`. |
| WATCH | Note in the thesis file under `## Notes & Updates`. Schedule for next Quarterly review. No trade. |
| CLEAR | "No action taken — by design." |

## Intellectual Honesty

If a scanned document does not contain data relevant to a Kill Condition, mark that KC as CLEAR for this scan with a note:
> "[KC-1] Not evaluable from this document — relevant metric not present."

Never infer a CLEAR from absence of negative data. Only mark BREACH or WATCH when positive evidence exists.
