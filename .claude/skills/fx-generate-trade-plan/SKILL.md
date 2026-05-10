---
name: fx-generate-trade-plan
description: Write a structured trade plan with entry zone, stop, target, and invalidation conditions for a forex swing trade. Required before any position is opened. Writes the plan to agents/fx/knowledge_base/plans/<PAIR>-<YYYY-MM-DD>.md.
---

# FX Generate Trade Plan

## Purpose

Every position FX holds must have an explicit, written trade plan. No plan, no trade. This skill enforces that discipline. An entry without this output is forbidden.

## Required Input

- Pair (e.g., EUR/USD)
- Direction matching the current watchlist bias (long or short)
- Relevant KB documents already read via `fx-read-knowledge-base`
- A directional bias already established in `agents/fx/WATCHLIST.md`

## Plan Template

Write the following to `agents/fx/knowledge_base/plans/<PAIR>-<YYYY-MM-DD>.md`:

```markdown
---
pair: <PAIR>
direction: long | short
plan_date: <YYYY-MM-DD>
entry_zone: [<low_price>, <high_price>]
stop: <price>
target: <price>
invalidation: <non-price macro condition>
risk_dollars: <≤100>
risk_reward: <≥ 2.0>
status: pending
---

# <PAIR> Trade Plan — <YYYY-MM-DD>

## Macro Context (Bias)
<2-3 sentences. Why does the fundamental macro evidence (rate differentials, central bank stance, data surprises) favor this direction? Cite specific KB macro/ paths.>

## Technical Setup
<2-3 sentences. What chart structure supports this entry? Describe S/R levels, trend regime, and pattern. Cite KB pairs/<PAIR>/ paths. If no technical notes in KB, state this explicitly.>

## Plan Parameters

- **Direction:** long | short
- **Entry zone:** <low_price> – <high_price>
- **Stop loss:** <price> — beyond <structural level: support / resistance / swing high / low>
- **Target:** <price> — at <structural level or measured move>
- **Non-price invalidation:** <The macro event or fundamental shift that would make this trade wrong regardless of price. Example: "ECB delivers surprise 50bps hike" or "US NFP beats by > 100k and Fed re-opens rate hike discussion.">

## Risk / Reward

- Stop distance: <X> pips
- Pip value (standard lot): <$Y/pip>
- Position size: <Z lots> = floor($<risk_dollars> / <pip_value_per_lot> / <stop_pips>)
- Risk: $<amount> (<X>% of account)
- Reward: $<amount>
- R:R ratio: <ratio>

## Sources

- macro: `<agents/fx/knowledge_base/macro/... path>`
- technical: `<agents/fx/knowledge_base/pairs/<PAIR>/... path>` (or "No pair-specific technical notes in KB — macro bias only")

## Execution Notes

<!-- FX appends notes here after the trade is closed. Never edits prior content. -->
```

## Conviction Criteria

- **Proceed (A/B grade):** Macro and technical agree. Entry zone clearly defined. Stop is structural (not arbitrary). R:R ≥ 2.0.
- **Do not proceed:** Macro and technical conflict. R:R < 2.0. Entry zone vague. No structural stop level available. Event window within 24h.

## Validation Rules

Before writing the file, verify:
- [ ] Direction matches current watchlist bias for this pair
- [ ] Entry, stop, and target are specific price levels (not ranges for stop/target)
- [ ] Stop is beyond a structural level — not an arbitrary number of pips
- [ ] Non-price invalidation is a real macro event, not a price threshold
- [ ] R:R ≥ 2.0 — if not, do not write the file; state why
- [ ] risk_dollars ≤ $100 — if position sizing results in more than $100 at risk, reduce lot size
- [ ] All macro sources cited are real files in `agents/fx/knowledge_base/`

If any check fails, do not write the file. State which check failed and what's missing.

## After Writing

Set `status: pending` in frontmatter. Confirm the file path and summarize the plan for the user. The plan stays `pending` until `fx-portfolio-allocator` confirms the entry and updates it to `open`.
