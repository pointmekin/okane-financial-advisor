You are running **FX_Daily_Brief** — the daily macro and calendar scan.

Invoke the `fx` subagent with these instructions:

---

This is the FX_Daily_Brief trigger. Your job is to scan new KB inputs, flag upcoming events, and update pair biases. **You never open trades in this trigger.** This is a read-and-update cycle only.

**Step-by-step:**

1. **Check last review date** — Read `agents/fx/portfolio.md` and note `last_reviewed`. If this trigger has been run in the last 12 hours, state: "FX_Daily_Brief was already run today. Run it once per day." Then stop.

2. **Scan new KB inputs** — Invoke the `fx-read-knowledge-base` skill, scoped to documents added since `last_reviewed`. If no new documents exist, state: "No new KB inputs since last brief. No bias changes." Update `last_reviewed` in `agents/fx/portfolio.md` and stop.

3. **Event calendar check** — Scan `agents/fx/knowledge_base/calendar/` for high-impact events in the next 24–48 hours. For any pair with a scheduled event involving its currencies, invoke `fx-watchlist-manager` to set that pair to `no-trade` with a note explaining the event and timing.

4. **Bias updates** — For any pair with new KB documents, reassess the directional bias. Invoke `fx-watchlist-manager` to update bias, setup grade, and notes. Bias must be grounded in the new KB documents — not in price movement.

5. **Trigger evaluation** — Invoke the `fx-evaluate-triggers` skill against new KB documents and all open positions in `agents/fx/portfolio.md`. Report any WATCH or BREACH alerts. Do not trade — just report.

6. **Update dates** — Update `last_reviewed` in `agents/fx/portfolio.md` and `agents/fx/WATCHLIST.md`.

7. **Output** — Print a brief daily summary:
   - New KB documents ingested
   - Event-window flags (pairs set to `no-trade` and why)
   - Bias changes (pairs where directional lean changed)
   - Any trigger alerts from open positions

**Hard rules for this trigger:**
- No trades — this is a monitoring cycle only
- Bias changes must be grounded in KB content, not price action
- "Prices moved overnight" is never a reason to change bias
