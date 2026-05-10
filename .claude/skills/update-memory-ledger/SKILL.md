---
name: update-memory-ledger
description: Append a reflection, broken thesis, or lesson learned to Z.md. Use when Nick's original investment thesis is proven wrong by reality, after a forced exit, or after a significant analytical mistake. Never edits past entries — append only.
---

# Update Memory Ledger

## Purpose

`agents/nick/Z.md` is Nick's long-term mistake ledger. Inspired by open-source AI memory concepts, it is the mechanism by which Nick compounds his analytical judgment over time — not by fixing prices, but by fixing thinking patterns.

## When to Write a New Entry

Trigger this skill when:
- A Kill Condition is officially breached and the position is exited
- A Quarterly review concludes "thesis broken"
- Nick realizes post-hoc that his original analytical framing was wrong
- A significant omission or bias is identified in past reasoning

**Do NOT write an entry for:**
- Normal quarterly reviews where the thesis remains intact
- Positions that were exited for reasons of capital rotation into higher-conviction opportunities (no mistake involved)

## Ledger Location

`agents/nick/Z.md`

## Append-Only Rule

NEVER edit or delete existing entries. Every entry is permanent. The ledger is a record of intellectual honesty. New entries are always appended to the bottom.

## Entry Format

```markdown
---

## <YYYY-MM-DD> — <TICKER> (<Company Name>)

### Original Thesis
<1-3 sentences. Reproduce the core thesis as it was written at time of investment — verbatim from the thesis file if possible.>

### Kill Condition Triggered
> **[KC-X]** <Paste the exact kill condition that was triggered, or describe the analytical failure if no KC was formally triggered.>

### Reality — What Actually Happened
<2-4 sentences. Factual account of what the business did that contradicted the thesis. Cite specific data points from KB documents.>

### Lesson Learned
<1-3 sentences. State the specific analytical trap, bias, or omission that caused the mistake. Be precise — generic lessons like "do more research" are not useful. Examples: "I over-weighted management commentary without checking whether capital allocation behavior matched their words." / "I failed to account for the regulatory tail risk documented in the 10-K risk factors section.">

### Impact on Future Analysis
<Optional. If this lesson should change a specific checklist item or evaluation criterion, state it here.>
```

## Procedure

1. Read the current `agents/nick/Z.md` to find the last entry
2. Compose the new entry following the format above
3. Append to the bottom of `agents/nick/Z.md` (after the last `---` separator)
4. Confirm the entry was written with the date and ticker

## Forbidden Actions

- Editing any line above the newly appended entry
- Removing or softening the "Kill Condition Triggered" section to save face
- Writing vague lessons ("I should have been more careful") — require specificity
