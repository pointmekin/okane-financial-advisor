You are running **FX_Init** — the one-time initialization of the FX agent.

Invoke the `fx` subagent with these instructions:

---

This is the FX_Init trigger. Your job is to read the FX Knowledge Base, establish directional bias for each watched pair, and initialize the watchlist. **You do not open any positions during Init.** FX starts flat.

**Step-by-step:**

1. **Read the Knowledge Base** — Invoke the `fx-read-knowledge-base` skill. Scan every document in `agents/fx/knowledge_base/macro/`, `agents/fx/knowledge_base/pairs/`, `agents/fx/knowledge_base/articles/`, and `agents/fx/knowledge_base/calendar/`. Read each document in full. Produce a structured summary per document.

2. **Check for empty KB** — If the Knowledge Base is empty or contains fewer than 1 substantive document, stop and state: "I don't know — the FX Knowledge Base is empty. I cannot establish directional bias without source documents. Please add macro reports, central bank statements, or pair-specific notes to `agents/fx/knowledge_base/` before running fx-init."

3. **Establish pair bias** — For each of the seven default majors (EUR/USD, GBP/USD, USD/JPY, USD/CHF, AUD/USD, NZD/USD, USD/CAD), form a directional bias based on the KB documents read. Bias must be one of: `long-bias`, `short-bias`, `neutral`, `no-trade`. If insufficient KB research exists for a pair, default to `neutral` and note what's missing.

4. **Check for high-impact events** — Scan `agents/fx/knowledge_base/calendar/` for any events scheduled within the next 24 hours. Any pair whose currencies are involved gets temporary `no-trade` status until the event window closes.

5. **Update the watchlist** — Invoke the `fx-watchlist-manager` skill to update bias, setup grade, and notes for each pair in `agents/fx/WATCHLIST.md`. Update `last_reviewed` date.

6. **Confirm** — Print a summary: pair-by-pair bias table, any event-window `no-trade` flags, and what KB documents were read. State which pairs have insufficient research.

**Hard rules for this trigger:**
- Do not open any positions during Init — bias formation only
- Do not form a bias on any pair without at least one KB document supporting it
- Do not run this trigger more than once unless explicitly resetting from scratch
