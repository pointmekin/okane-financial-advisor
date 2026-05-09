---
name: watchlist-manager
description: Manage WATCHLIST.md — add new tickers, update research status, promote tickers to ready, and pass on tickers that don't meet the bar. Use when Nick needs to add a candidate, review watch list progress, or decide whether sufficient KB research exists to form a thesis.
---

# Watchlist Manager

## Watchlist Location

`WATCHLIST.md` — at the project root.

## Status Lifecycle

```
watching → researching → ready → invested
                       ↘ passed
```

- **watching → researching**: when at least 1 KB document exists for the ticker
- **researching → ready**: when Nick judges there is sufficient evidence to write a full thesis (at least 1 filing or transcript + 1 corroborating article/note)
- **researching → passed**: when research reveals the business does not meet Nick's quality bar
- **ready → invested**: when `/nick-init` or `/nick-weekly` allocates capital and opens a position
- **any → passed**: can happen at any stage if the business quality case falls apart

## Actions

### Add a ticker to the watch list

Append a row to the `## Watch List` table in `WATCHLIST.md`:

```markdown
| TICKER | Full Company Name | <1-sentence why it's interesting> | YYYY-MM-DD | watching | — |
```

Rules:
- "Why Watching" must be a business-quality hypothesis, not a price observation. Good: "Capital-light compounder with scale advantages in industrial distribution." Bad: "Stock is down 30%, looks cheap."
- If KB documents already exist for this ticker, set status to `researching` and list them in the KB Docs column.

### Update status

Edit the row's `Status` cell. Update the `KB Docs` column to list any relevant files added to `knowledge_base/`.

### Promote to ready

Set status to `ready` when ALL of the following are true:
- At least 1 primary source (10-K, 10-Q, or earnings transcript) exists in the KB for this ticker
- At least 1 corroborating source (article, analyst note, or personal thesis note) exists
- Nick has read both and judges the evidence sufficient to write Kill Conditions

When promoting, add the KB doc paths to the `KB Docs` column.

### Pass on a ticker

1. Change status to `passed` in the Watch List table
2. Move the row to the `## Passed Tickers` table with a `Reason Passed` entry
3. Reason must be specific: "Gross margins structurally below 40% — commodity business, no pricing power." Not: "Not interesting enough."

### Mark as invested

When a position is opened via `portfolio-allocator`:
1. Set status to `invested` in the Watch List table
2. Add a link to `knowledge_base/theses/<TICKER>.md` in the KB Docs column

## Weekly Watchlist Review (called by Nick_Weekly)

For each ticker with status `watching` or `researching`:
1. Check if new KB documents were added since `last_reviewed` in `WATCHLIST.md`
2. If yes: update status and KB Docs column; promote to `ready` if threshold is met
3. If no new docs: leave status unchanged — no nagging, no reminders

Output a brief watchlist status report:
```markdown
### Watchlist Review — <YYYY-MM-DD>
- **TICKER** (researching → ready): sufficient evidence now exists. Recommend generating thesis during next Init or as a portfolio addition if a position slot is available.
- **TICKER** (watching): no new KB docs added this week.
- **TICKER** (passed): no change.
```

Update `last_reviewed` in the `WATCHLIST.md` frontmatter after every review.
