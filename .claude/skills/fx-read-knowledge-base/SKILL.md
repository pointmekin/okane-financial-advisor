---
name: fx-read-knowledge-base
description: Scan and read documents from the FX agent's Knowledge Base. Use when FX needs to ingest macro reports, central bank statements, economic calendar events, or pair-specific technical notes before forming a directional bias or trade plan. Returns structured macro/technical summaries and uncertainty flags.
---

# FX Read Knowledge Base

## Knowledge Base Structure

The FX KB lives at `agents/fx/knowledge_base/` with five typed subdirectories:

| Folder | Contents | File naming |
|--------|----------|-------------|
| `agents/fx/knowledge_base/macro/` | Central bank statements, rate decisions, CPI/NFP/PMI releases, economic commentary | `<PAIR-or-general>-<YYYY-MM-DD>-<tag>.md` |
| `agents/fx/knowledge_base/pairs/<PAIR>/` | Pair-specific technical notes, COT data, correlation observations | `<PAIR>-<YYYY-MM-DD>-<tag>.md` |
| `agents/fx/knowledge_base/calendar/` | Upcoming high-impact event schedules | `calendar-<YYYY-MM-DD>.md` |
| `agents/fx/knowledge_base/plans/` | Active and historical trade plans — read-only here | `<PAIR>-<YYYY-MM-DD>.md` |
| `agents/fx/knowledge_base/articles/` | Analyst views, research notes, broader commentary | `<PAIR-or-general>-<YYYY-MM-DD>-<tag>.md` |

## How to Scan

### Full KB scan (used by FX_Init)
```
Glob: agents/fx/knowledge_base/**/*.md (exclude plans/)
Read each file in full — macro reports and central bank statements must be read start to finish
```

### Scoped scan (used by FX_Daily_Brief and FX_Weekly)
```bash
find agents/fx/knowledge_base/ -name "*.md" -newer agents/fx/portfolio.md -not -path "*/plans/*"
```

### Pair-specific scan (used by FX_Setup_Scan and FX_Trade)
```bash
find agents/fx/knowledge_base/pairs/<PAIR>/ agents/fx/knowledge_base/macro/ -name "*.md"
```

## Output Format

For each document read, produce:

```markdown
### Source: <relative path>
**Type:** macro | technical | calendar | article
**Pair(s):** EUR/USD, USD/JPY, ... (or "general")
**Date:** YYYY-MM-DD

#### Macro Context Extracted
- Rate differential: <status>
- Central bank stance: <hawkish | dovish | neutral>
- Last data surprise: <beat | miss | in-line> on <indicator>
- Key upcoming risk: <event and date>
- (list only items present in source — never interpolate)

#### Technical Observations (if present)
- Trend regime: <uptrend | downtrend | ranging>
- Key S/R levels: <levels>
- Pattern / setup: <description>
- COT positioning: <net long/short and change>

#### High-Impact Events (calendar docs only)
- <Date> — <Currency> — <Event> — <Expected impact: HIGH | MEDIUM>

#### Uncertainty Flags
- [FLAG] Data not present in source: <what's missing>
- [FLAG] Conflicting signals: <what conflicts>
```

## Intellectual Honesty Requirement

If a file is missing or empty:
> "I don't know — `<path>` is absent from the FX Knowledge Base. I cannot form a directional view without reading source documents."

Never fill gaps with training-data knowledge of real central banks, economic data, or price levels. All analysis must come from files in `agents/fx/knowledge_base/`.
