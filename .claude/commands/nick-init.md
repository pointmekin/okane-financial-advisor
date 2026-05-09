You are running **Nick_Init** — the one-time initialization of Nick's $10,000 virtual portfolio.

Invoke the `nick` subagent with these instructions:

---

This is the Nick_Init trigger. Your job is to read the entire Knowledge Base, form investment theses for 3-10 high-conviction businesses, and deploy the initial $10,000 capital.

**Step-by-step:**

1. **Read the Knowledge Base** — Invoke the `read-knowledge-base` skill. Scan every document in `knowledge_base/transcripts/`, `knowledge_base/filings/`, and `knowledge_base/articles/`. Read each document in full. Produce a structured summary per document.

2. **Check for empty KB** — If the Knowledge Base is empty or contains fewer than 1 substantive document, stop and state: "I don't know — the Knowledge Base is empty. I cannot form investment theses without reading source documents. Please add research to `knowledge_base/` before running Nick_Init."

3. **Check the watch list** — Invoke the `watchlist-manager` skill. Read `WATCHLIST.md`. For any ticker with status `ready` or `researching`, cross-reference against the KB documents just read. Promote any `researching` tickers to `ready` if sufficient evidence now exists. Tickers with `ready` status are your primary thesis candidates.

4. **Form theses** — For each business you find sufficient evidence to evaluate (from KB docs and/or `ready` watchlist tickers), invoke the `generate-thesis-and-kill-conditions` skill. Only proceed with businesses where you have high or medium conviction from the source documents. Select 3-10 names total.

5. **Allocate capital** — Once all theses are written, invoke the `portfolio-allocator` skill to allocate the $10,000 across your selected positions. Allocate by conviction strength, not by price dynamics. Run all invariant checks before writing `portfolio.md`. For any invested ticker, update its status to `invested` in `WATCHLIST.md` via `watchlist-manager`.

6. **Confirm** — Print a summary: number of positions, cash remaining, and a one-line thesis for each position. Also print the updated watchlist status for any tickers promoted during this run.

**Hard rules for this trigger:**
- Ignore current market prices when sizing positions — allocate by conviction percentage first
- Do not form a thesis on any company not represented in the Knowledge Base
- Do not run this trigger more than once unless explicitly resetting the portfolio from scratch
