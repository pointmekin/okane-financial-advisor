# Nick Agent - Workflow Setup

To replicate the "Nick" AI agent workflow, you need to set up a specific pipeline for data ingestion, execution, and memory management.

## 1. The Knowledge Base (The Brain)
Create a centralized repository (folder or database) called the **Knowledge Base**. 
- Feed this with earning call transcripts, specific articles, analyst reports, and personal thesis notes.
- The agent will continuously read and compound its understanding from this base.

## 2. Execution Pipeline
The agent operates on three specific execution triggers:

### A. Nick_Init (Initialization)
- **Trigger:** Run once at the very beginning.
- **Action:** The agent reads the entire Knowledge Base and deploys the initial $10,000 capital. It selects 3-10 high-conviction stocks and sets the initial allocation. It ignores current market prices, focusing solely on long-term business value and conviction.

### B. Nick_Weekly (Weekly Review)
- **Trigger:** Run once every week (e.g., every weekend).
- **Action:** The agent reviews the portfolio against new weekly inputs in the Knowledge Base. 
- **Rule:** Daily checks are banned to avoid noise. Actions are only taken if a "Kill Condition" is triggered or a massive new conviction is found.

### C. Nick_Quarterly (Earnings Review)
- **Trigger:** Run after earning releases for held stocks.
- **Action:** The agent reads the new earnings filings and transcripts. It strictly evaluates whether the original investment thesis remains intact, strengthened, or broken.

## 3. The Memory System: Z.md
Create a Markdown file named `Z.md` (inspired by open-source AI memory concepts).
- **Purpose:** This is the agent's long-term memory and mistake ledger.
- **Action:** Whenever the agent makes a mistake (e.g., an original thesis is proven wrong), it must write down a reflection in `Z.md`. It records *why* it was wrong so it can learn and avoid the same analytical trap in the future.
