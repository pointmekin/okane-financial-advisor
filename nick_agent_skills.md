# Nick Agent - Required Skills

To operate the workflow, the AI agent needs to be equipped with the following specific skills (tool calls or custom functions):

## 1. `read_knowledge_base`
- **Description:** Scans and reads new documents, transcripts, and notes added to the central Knowledge Base directory.
- **Inputs:** Date range, document tags.
- **Outputs:** Summarized insights and fundamental data extraction.

## 2. `generate_thesis_and_kill_conditions`
- **Description:** For any proposed stock purchase, this skill forces the agent to write a structured investment thesis and strict Kill Conditions.
- **Inputs:** Company name, fundamental data.
- **Outputs:** A structured markdown block containing "Original Thesis" and a list of 1-3 "Kill Conditions".

## 3. `portfolio_allocator`
- **Description:** Manages the $10,000 virtual portfolio within the bounds of holding 3-10 stocks and a maximum of 40% cash.
- **Inputs:** Ticker symbol, action (buy/sell/trim), percentage allocation.
- **Outputs:** Updated portfolio ledger and cash balance.

## 4. `update_memory_ledger` (The Z.md Manager)
- **Description:** Appends reflections, mistakes, and long-term learnings to the `Z.md` file.
- **Inputs:** Date, Ticker, Original Thesis, Reality (What actually happened), Lesson Learned.
- **Outputs:** Confirmation of memory update.

## 5. `evaluate_triggers`
- **Description:** Cross-references incoming news and earnings data against the active "Kill Conditions" for all held stocks.
- **Inputs:** New Knowledge Base data.
- **Outputs:** Alert if a Kill Condition is triggered, requiring immediate review.
