You are running **Nick_Quarterly** — the post-earnings thesis review for a specific stock.

Usage: `/nick-quarterly <TICKER>`

Invoke the `nick` subagent with these instructions:

---

This is the Nick_Quarterly trigger for **$ARGUMENTS**. Your job is to re-read all available filings and transcripts for this company and deliver a strict verdict on the original investment thesis.

**Step-by-step:**

1. **Load the original thesis** — Read `knowledge_base/theses/$ARGUMENTS.md`. If it doesn't exist, state: "No thesis found for $ARGUMENTS. Either this ticker is not held, or Nick_Init has not been run."

2. **Read new earnings material** — Invoke the `read-knowledge-base` skill, filtered to `$ARGUMENTS` across all KB folders. Focus on the most recent earnings transcript and the latest 10-Q/10-K.

3. **Check Kill Conditions** — Invoke the `evaluate-triggers` skill with the newly read documents and the Kill Conditions from the thesis. Document evidence for each KC.

4. **Deliver a verdict** — Produce a structured evaluation:

```markdown
## Quarterly Review — $ARGUMENTS — <YYYY-MM-DD>

### Verdict: INTACT | STRENGTHENED | BROKEN

### Thesis Status
<1-2 sentences on whether the core business argument still holds, and why.>

### Kill Condition Review
- [KC-1] <status: CLEAR / WATCH / BREACH> — <evidence>
- [KC-2] <status: CLEAR / WATCH / BREACH> — <evidence>

### Key Changes Since Last Review
- <Bullet list of new data points that materially update the view>

### Action
<No action | Trim | Add | Exit — with one-sentence justification>
```

5. **If verdict is BROKEN:**
   - Invoke `portfolio-allocator` to exit the position
   - Update thesis `status` to `broken`
   - Invoke `update-memory-ledger` to append a dated reflection to `Z.md`

6. **If verdict is INTACT or STRENGTHENED:**
   - Append a dated note to the `## Notes & Updates` section of the thesis file
   - Update `last_reviewed` in the thesis frontmatter
   - No trade required

**Hard rules for this trigger:**
- The verdict must be grounded solely in KB documents for this ticker
- "Thesis broken" requires specific evidence, not general market concern
- Never upgrade conviction based on price appreciation alone
