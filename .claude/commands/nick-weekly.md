You are running **Nick_Weekly** — the weekly review cycle.

Invoke the `nick` subagent with these instructions:

---

This is the Nick_Weekly trigger. Daily checks are **explicitly banned**. This review happens once per week (typically on weekends). Your default posture is: **do nothing**.

**Step-by-step:**

1. **Check last review date** — Read `agents/nick/portfolio.md` and note the `last_reviewed` date. If this trigger has been run less than 5 days ago, state: "Nick_Weekly was already run on <last_reviewed>. Daily checks are banned. Come back on the weekend." Then stop.

2. **Scan new KB inputs** — Invoke the `read-knowledge-base` skill, scoped to documents added since `last_reviewed`. If no new documents exist, state: "No new Knowledge Base inputs since last review. No action — by design." Update `last_reviewed` in `agents/nick/portfolio.md` and stop.

3. **Evaluate triggers** — Invoke the `evaluate-triggers` skill against the new KB documents and all active Kill Conditions.

4. **Review the watch list** — Invoke the `watchlist-manager` skill to run the weekly watchlist review. For any watchlist ticker that has new KB docs, update its status. Promote to `ready` if the evidence threshold is met. This step never triggers a trade — it only updates `agents/nick/WATCHLIST.md`.

5. **Decision gate:**
   - If **no BREACH** and **no `ready`-status ticker with a portfolio slot available** and **no overwhelming new conviction opportunity**: state "No action taken — by design." Update `last_reviewed` in both `agents/nick/portfolio.md` and `agents/nick/WATCHLIST.md` and stop.
   - If **BREACH detected**: read the full source document. If breach is confirmed, exit the position via `portfolio-allocator`, record the lesson in `agents/nick/Z.md` via `update-memory-ledger`. If the exited position creates a slot, check `agents/nick/WATCHLIST.md` for any `ready` tickers before concluding.
   - If a **`ready`-status ticker** exists and a portfolio slot is available (positions < 10): invoke `generate-thesis-and-kill-conditions`, then `portfolio-allocator`. Update the ticker's watchlist status to `invested` via `watchlist-manager`.
   - If **massive new conviction opportunity** found in KB (not on watchlist): invoke `watchlist-manager` to add it, then immediately proceed to `generate-thesis-and-kill-conditions` and `portfolio-allocator` if conviction is absolute.

**Hard rules for this trigger:**
- The bias must always be toward inaction
- A WATCH-level alert does not trigger a trade — only a BREACH does
- A `ready` ticker does not auto-invest — there must be a portfolio slot AND Nick must judge conviction high enough
- "The stock went up/down this week" is never a reason to act
