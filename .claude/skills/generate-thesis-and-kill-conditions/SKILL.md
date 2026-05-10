---
name: generate-thesis-and-kill-conditions
description: Force-structure an investment thesis and 1-3 Kill Conditions for any proposed stock purchase. Use before any buy decision. Writes the completed thesis file to knowledge_base/theses/<TICKER>.md.
---

# Generate Thesis and Kill Conditions

## Purpose

Every position Nick holds must have an explicit, written thesis and at least one Kill Condition. This skill enforces that discipline. A buy without this output is forbidden.

## Required Input

- Company name and ticker
- Relevant KB documents already read via `read-knowledge-base`
- Nick's formed view on the business

## Output Template

Write the following to `knowledge_base/theses/<TICKER>.md`:

```markdown
---
ticker: <TICKER>
company: <Full Company Name>
thesis_date: <YYYY-MM-DD>
last_reviewed: <YYYY-MM-DD>
status: active | broken | exited
conviction: high | medium
---

# <TICKER> -- Investment Thesis

## Original Thesis
<2-4 sentences. State the core business quality argument: what makes this business exceptional, what durable advantage it holds, why it compounds value over time. Focus on the business, not the price.>

## Why Now (Opportunity)
<1-2 sentences. What specific condition makes this a good time to allocate capital? Avoid price reasoning — focus on business milestones, competitive dynamics, or management actions.>

## Key Business Metrics to Track
- <Metric 1>: current value, directional expectation
- <Metric 2>: current value, directional expectation
- (2-4 metrics only — the ones that matter most)

## Kill Conditions
> Kill Conditions are specific, fundamental, and measurable. They are NOT price targets or drawdown thresholds. Triggering one requires immediate review and likely exit.

1. **[KC-1]** <Specific condition that, if true, proves the original thesis wrong. Example: "Gross margin falls below 55% for two consecutive quarters, indicating structural pricing pressure.">
2. **[KC-2]** <Second condition — optional but recommended. Example: "Management diverts >20% of FCF to unrelated acquisitions outside the core business.">
3. **[KC-3]** <Third condition — optional. Only include if genuinely distinct from KC-1 and KC-2.>

## Sources
- `<path to KB document 1>`
- `<path to KB document 2>`
(List every KB document read to form this thesis — intellectual honesty requires it)

## Conviction Level
**<High | Medium>** — <One sentence justifying the conviction level based on quality and completeness of source material>

## Notes & Updates
<!-- Nick appends dated notes here after each Quarterly review. Never edits prior entries. -->
```

## Conviction Level Criteria

- **High**: Multiple primary sources (10-K + 10-Q or transcript) plus corroborating analysis; business model well-understood; clear competitive moat with quantifiable metrics; few or no conflicting signals
- **Medium**: At least one primary source plus one corroborating source; thesis is supportable but has identifiable gaps (e.g., limited historical data, execution risk on stated strategy, or meaningful conflicting analyst signals)
- Never assign conviction below Medium — if evidence is thinner than this, do not write a thesis

## Validation Rules

Before writing the file, verify:
- [ ] Original Thesis is grounded in business fundamentals from KB docs (not price action)
- [ ] At least 1 Kill Condition is present and measurable (not "if the stock drops 30%")
- [ ] All sources listed are real files in `knowledge_base/`
- [ ] No hallucinated financials — every number cites a source
- [ ] Conviction level is justified per the criteria above

If any check fails, do not write the file. State which check failed and what data is missing.

## After Writing

Confirm the file is written at `knowledge_base/theses/<TICKER>.md` and summarize the thesis + kill conditions for the user in plain English.
