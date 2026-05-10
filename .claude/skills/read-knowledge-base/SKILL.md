---
name: read-knowledge-base
description: Scan and read documents from the Nick agent's Knowledge Base. Use when Nick needs to ingest transcripts, filings, articles, or thesis notes before forming or reviewing an investment view. Returns summarized fundamentals, extracted data points, and uncertainty flags.
---

# Read Knowledge Base

## Knowledge Base Structure

The KB lives at `knowledge_base/` with four typed subdirectories, each grouped by ticker:

| Folder | Contents | Path pattern | File naming |
|---|---|---|---|
| `transcripts/<TICKER>/` | Earnings call transcripts | `transcripts/GOOG/GOOG-2025-01-30-transcript.md` | `<TICKER>-<YYYY-MM-DD>-transcript.md` |
| `filings/<TICKER>/` | 10-K, 10-Q, annual reports | `filings/GOOG/GOOG-2025-12-31-10K.md` | `<TICKER>-<YYYY-MM-DD>-<10K\|10Q>.md` |
| `articles/<TICKER>/` | Analyst reports, news, research notes | `articles/GOOG/GOOG-2025-05-01-earnings-note.md` | `<TICKER>-<YYYY-MM-DD>-<tag>.md` |
| `theses/` | Nick's own thesis files (one per ticker) | `theses/GOOG.md` | `<TICKER>.md` |

General (non-ticker) articles go in `articles/general/` with naming `general-<YYYY-MM-DD>-<tag>.md`.

## How to Scan

### Full KB scan (used by Nick_Init)
```
Glob: knowledge_base/**/*.md
Read each file in full — transcripts and filings must be read start to finish, never skimmed
```

### Scoped scan (used by Nick_Weekly and Nick_Quarterly)
- For **Nick_Weekly**: scan only files modified after the `last_reviewed` date in `portfolio.md`
- For **Nick_Quarterly**: filter to a specific ticker across all four folders

To find recent files:
```bash
find knowledge_base/ -name "*.md" -newer portfolio.md -not -path "*/theses/*"
```

## Output Format

For each document read, produce:

```markdown
### Source: <relative path>
**Type:** transcript | filing | article | thesis
**Ticker(s):** AAPL, MSFT, ...
**Date:** YYYY-MM-DD

#### Key Fundamentals Extracted
- Revenue growth: X% YoY
- Operating margin: X%
- FCF conversion: X%
- Debt/equity: X
- (list only items present in source — never interpolate)

#### Management Commentary Highlights
- "<direct quote>" — context

#### Uncertainty Flags
- [FLAG] Data not present in source: <what's missing>
- [FLAG] Conflicting signals: <what conflicts>
```

## Intellectual Honesty Requirement

If a file is missing or empty:
> "I don't know — `<path>` is absent from the Knowledge Base. I cannot form a view without reading the source document."

Never fill gaps with training-data knowledge of real companies. All analysis must come from files in `knowledge_base/`.
