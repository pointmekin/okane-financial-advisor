---
name: fx-update-memory-ledger
description: Append a reflection about an execution mistake or rule violation to agents/fx/Z.md. Use when a trade was opened against a rule, a stop was widened, a plan was abandoned, or three consecutive losses occurred without review. Never write for clean stop-outs.
---

# FX Update Memory Ledger

## Purpose

`agents/fx/Z.md` is the FX agent's execution mistake ledger. It records process violations — the moments when FX broke its own rules — so the same mistake is never made twice.

## When to Write a New Entry

**Write an entry when:**
- A position was opened without a written plan (rule violation)
- A correlated overlap existed but wasn't caught before entry (invariant failure)
- A stop was widened at any point during the trade (the most important rule)
- A plan was abandoned without a closure note being appended
- Three consecutive closed losses occurred without running `/fx-weekly` to review macro stance
- A non-price invalidation condition was triggered but missed and only caught post-hoc

**Do NOT write an entry for:**
- A clean stop-out — price hit the stop, and the stop was honored. This is the system working correctly.
- A trade that didn't reach its target — an intact thesis and a disappointed result is not a mistake.
- A trade closed at break-even or small profit — no mistake, no entry.

## Ledger Location

`agents/fx/Z.md` — at the agent root.

## Append-Only Rule

NEVER edit or delete existing entries. Every entry is permanent. The ledger is a record of execution discipline. New entries are always appended to the bottom.

## Entry Format

```markdown
---

## <YYYY-MM-DD> — <PAIR> (<Trade Direction>)

### Trade Context
<1-2 sentences. Entry price, stop, target, and brief direction of the trade. Purpose: establish context for the mistake.>

### Rule Violated
> **[Rule]** <State the exact rule that was broken. Examples: "Stop was widened from X to Y on <date>." / "Position opened without a written plan in knowledge_base/plans/." / "Entered EUR/USD long while USD/CHF short was already open — correlated overlap.">

### What Happened
<2-3 sentences. Factual account of the execution error. What was done, when, and what the result was. Do not editorialize — just facts.>

### Lesson Learned
<1-2 sentences. Specific behavioral change or process check to prevent recurrence. Be precise — generic lessons are useless. Examples: "Before any entry, verify all six invariants in sequence, not from memory." / "If stop needs moving, close the trade and re-enter with a new plan — never widen in place.">

### Process Change (Optional)
<If this lesson should change a specific invariant, checklist item, or step in a command, state it here. This is how the ledger compounds into better process over time.>
```

## Procedure

1. Read the current `agents/fx/Z.md` to find the last entry
2. Compose the new entry following the format above
3. Append to the bottom of `agents/fx/Z.md` (after the last `---` separator)
4. Confirm the entry was written with the date and pair

## Forbidden Actions

- Editing any line above the newly appended entry
- Writing vague lessons ("I should have been more careful") — require specificity
- Confusing a bad trade outcome with a bad process — outcomes are often random; process violations are always preventable
