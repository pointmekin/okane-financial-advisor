# Knowledge Base

This is the brain Nick reads before making any investment decision. Drop research here before running any trigger.

## Folder Structure

| Folder | What goes here | Naming convention |
|--------|---------------|-------------------|
| `transcripts/` | Earnings call transcripts, investor day transcripts | `<TICKER>-<YYYY-MM-DD>-transcript.md` |
| `filings/` | 10-K annual reports, 10-Q quarterly filings | `<TICKER>-<YYYY-MM-DD>-10K.md` or `-10Q.md` |
| `articles/` | Analyst reports, news articles, personal research notes | `<TICKER>-<YYYY-MM-DD>-<tag>.md` or `general-<YYYY-MM-DD>-<tag>.md` |
| `theses/` | Nick's auto-generated investment theses (one per ticker) | `<TICKER>.md` — written by Nick, not by you |

## How to Add Research

1. Paste or write the content into a `.md` file
2. Name it following the convention above (ticker + date + type)
3. Drop it into the right subfolder
4. Run the appropriate trigger (`/nick-init`, `/nick-weekly`, `/nick-quarterly`)

## Tips

- Plain text is fine — no special formatting required
- Copy-paste earnings call transcripts directly
- For 10-K/10-Q: the MD&A, Risk Factors, and Cash Flow sections are the most important
- You can wikilink to these from anywhere in your Obsidian vault using `[[okane-financial-advisor/knowledge_base/filings/AAPL-2024-10-01-10K]]`
- Nick reads every document in full — longer is better than shorter

## What Nick Does NOT Use

- Market price feeds (Nick is price-blind at Init)
- News about price movements or analyst target changes
- Earnings surprise % or beat/miss scores

Nick cares only about the underlying business.
